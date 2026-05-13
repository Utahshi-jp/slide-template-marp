# なぜ Marp × Claude Code でスライドが自動化できるのか

> このテンプレートが「どう」動くかは README.md と CLAUDE.md にある。
> このドキュメントは「なぜ」この構成に至ったかをまとめた、設計の経緯と概念の整理。

---

## 1. 根本概念: Markdown が "AI とスライドの共通言語" になる

スライド自動化の鍵は、**Marp が Markdown 入力で動くこと**にある。
これが「AI に書かせる ⇔ そのままスライドになる」という橋渡しを可能にしている。

### 1.1 構造的に AI と相性が良い理由

```
              [AI が得意なこと]            [スライドが必要な形式]
                    │                              │
                    ▼                              ▼
              Markdown 生成                 ビジュアル表示
                    │                              │
                    └────── Marp が橋渡し ────────┘
                          (Markdown → HTML/PDF)
```

| 観点 | PowerPoint / Keynote | Marp (Markdown) |
|---|---|---|
| 入力フォーマット | バイナリ / 独自XML | プレーンテキスト |
| AI による生成 | ❌ 不可能に近い | ⭕ 自然に書ける |
| 差分管理(Git) | ❌ 困難 | ⭕ 行単位で差分 |
| 構造化 | ❌ ドラッグ&ドロップ依存 | ⭕ 見出し階層が明確 |
| 制約定義 | ❌ 数値で縛れない | ⭕ 文字数・行数で機械チェック可 |
| テーマ変更 | 個別操作 | CSS 1 ファイル |

**結論**: スライドが Markdown で書ける時点で、AI が自然言語からスライドを生成する障壁は事実上消えている。
あとは「**どんな見た目に仕上げるか**」を CSS とプロンプトで縛るだけ。

### 1.2 一段抽象化すると

「自動化の本質」はもっとシンプルで:

> **どんな成果物も、テキスト形式で表現できるなら AI が生成できる**

- スライド = Markdown(Marp) → AI が書ける
- グラフ = テキスト(Mermaid/PlantUML) → AI が書ける
- インフラ = HCL(Terraform) → AI が書ける
- UI = JSX → AI が書ける

逆に、バイナリやドラッグ操作が前提のフォーマットは、AI に任せる難易度が跳ね上がる。
これは LT スライドに限らず、**ツール選定の汎用原則**として覚えておく価値がある。

---

## 2. 4 点セット設計思想

このテンプレが採用した「再現性のあるスライド生成」のための仕組み。
**デザインシステムをそっくり持ち込む**発想で組んでいる。

| 役割 | 担当ファイル | フロント開発で言うと |
|---|---|---|
| コンポーネントライブラリ | `themes/theme.css` | shadcn/ui, MUI |
| Design Tokens / Rules | `CLAUDE.md` | Tailwind config |
| Storybook(実装例) | `templates/YYYYMMDD_template.md` | Storybook |
| ジェネレータ | `.claude/skills/slide-generate/SKILL.md` | codegen, Plop.js |

### なぜこの 4 つが必要か

- **コンポーネントだけ**用意しても、AI は使い方を知らない → Storybook が要る
- **Storybook だけ**あっても、いつどれを使うかルールが要る → Tokens が要る
- **ルールだけ**あっても、実行する仕組みが要る → ジェネレータが要る
- **ジェネレータだけ**あっても、出力品質はバラつく → コンポーネントが要る

→ 4つそろって初めて「AIに**安定して**スライドを生成させる」が成立する。

---

## 3. 会話の経緯(プロンプト & 回答の要約)

### Q1. 「[CLAUDE.md](http://CLAUDE.md)、Skills でスライド生成環境を構築したい。cursor-to-marp と Zenn 記事を参考に手法を提案して」

**回答要約**:

- 両資料を Web/gh CLI で取り込み、構造を把握
- Cursor-to-marp = 最小構成(`.cursor/rules`, `input/`, `YYYYMMDD_template.md`)
- Zenn 記事(loglass kagaya) = デザインシステム思想 + `.claude/commands/` + Puppeteer MCP
- 4 つの方針を質問(CLAUDE.md の扱い / コマンド方式 / Marp 実行環境 / テーマ)
- 全部の良いとこ取りプランを作成

**ユーザの選択**:
- CLAUDE.md はスライド生成専用に置き換え
- Skills + slash commands 両方併用
- Marp CLI + Puppeteer MCP
- 自作テーマ(themes/theme.css)

---

### Q2. 「Marp と slidev の比較をしてください」

**回答要約**:

- **AI 自動生成の文脈なら Marp 一択** という結論
- 比較表(構文/テーマ/インタラクティブ/エクスポート/学習コスト)
- Slidev の強みはインタラクティブ・Vue ベースのコンポーネント
- Slidev は AI 生成と相性が悪い理由(Vue 構文の誤り、テーマが npm パッケージ、HMR 前提)

---

### Q3. 「フロントエンドの学習も含めるなら Slidev は賢い判断?」

**回答要約**:

- 「学習目的なら賢い、生産性目的なら微妙」
- Slidev で学べるもの(Vue3, Vite, UnoCSS, コンポーネント設計, アニメ)
- 注意点(ユーザは React/Next 宣言済み → Vue は逆方向の学び、沼にハマる時間リスク)
- 折衷案: **Marp を本番運用 / Slidev を学習ブランチ**で並走

---

### Q4. 「3(両方並走)」

**回答要約**:

- 即座に実装着手
- ディレクトリ骨組み作成(input/images/resources/output/themes/templates/.claude/commands)
- CLAUDE.md を全面書き換え(スライド生成専用)
- themes/theme.css に 16 個の CSS コンポーネント(metric-card, strategy-grid 他)
- templates/YYYYMMDD_template.md(Storybook 相当)
- templates/script_template.md(台本テンプレ)
- Skills × 4(slide-generate, slide-design-system, slide-review, slide-script)
- slash commands × 5(generate-slides, research-and-slides, validate-slides, review-slides, export-pdf)
- README.md に Slidev 派生計画を明記
- input/input.md にサンプル投入

---

### Q5. 「サンプルで動作を確認したい」

**回答要約**:

- input/input.md からスライドを生成
- 初回: 制約違反 5/14 枚(行数オーバー / max line 41字)
- HTML ブロックを 1 行化 + 長文短縮で**自動修正** → 準拠率 100%
- 台本生成(1 スライド ≒ 90〜120 字でペーシング)
- Marp CLI で PDF 出力成功(278 KB)
- PNG 14 枚出力 → 画像認識で代表 4 枚をビジュアルレビュー
- 課題: `.comparison-matrix` の width: 100% が効いていない

---

### Q6. 「start で PDF を開きたい」

**回答要約**:

- Git Bash で `start` は動かない
- `powershell -c "Invoke-Item ..."` で代替
- `explorer` は exit 1 を返すが動作するという豆知識

---

### Q7. 「input.md を見せて」

**回答要約**:

- 当時の input.md の全文を提示
- 構造のポイントを箇条書きで説明

---

### Q8. 「input.md の構造テンプレって決まってますか?」

**回答要約**:

- **正直な答え: 決まっていない**(サンプルがデファクト)
- スライド/台本はテンプレ化済み、入力だけ未テンプレ化を発見
- 改善案 A(input_template.md を新規作成)/ B(frontmatter で構造化)
- **ハイブリッド推奨**: frontmatter + 自由記述

---

### Q9. 「作ってください」

**回答要約**:

- `templates/input_template.md` を新規作成(frontmatter スキーマ + コメント付き)
- `input/input.md` を frontmatter 形式にリファクタ
- `.claude/skills/slide-generate/SKILL.md` の Step 1 を「frontmatter を必ず読む」に修正
- `CLAUDE.md` に入力スキーマを追記
- frontmatter のパース動作確認 → 必須5項目すべて埋まる

---

### Q10. 「テンプレの内容を後から変えても生成に影響しない?」

**回答要約**:

- **影響します(限定的だが)**
- スキーマが 3 箇所(CLAUDE.md / SKILL.md / input_template.md)に重複定義されていた
- **DRY 違反** = Single Source of Truth が無い設計上の弱点
- 改善案 A(現状維持)/ B(SSoT 化)/ C(自動同期)
- B 推奨

---

### Q11. 「案 B を詳しく説明」

**回答要約**:

- SSoT(Single Source of Truth)の概念
- Before/After のコード例
- メリット(保守性、整合性、拡張性、オンボーディング)
- デメリット(Read コスト、CLAUDE.md の一覧性、テンプレ破損リスク)
- 堅牢化オプション(required/optional セクションでネスト)
- おすすめ: CLAUDE.md には**必須5項目の名前だけ**残し、詳細はテンプレに集約

---

### Q12. 「実装してください」

**回答要約**:

- `templates/input_template.md` に「★ SSoT」ヘッダと `schema_version: 1`、`# --- 必須 ---` `# --- 任意 ---` セクション
- `CLAUDE.md` のスキーマブロックを削除、要約のみ残す
- `.claude/skills/slide-generate/SKILL.md` の Step 1 を書き換え(テンプレを先に Read)
- **動作実証**: テンプレに `target_emotion` を1行追加 → 必須項目が5→6に追従 → input.md で欠落検出 → テンプレ復元
- スキーマ変更が**1ファイル編集で完結**することを物理的に証明

---

## 4. このプロジェクトで得た「設計上の学び」

このスライド生成テンプレ作りを通じて、AI協働の汎用原則として効くものをいくつか掴んだ。

### 4.1 「いい感じに」を排除して制約で縛る

- AI に「いい感じに作って」と言うとブレる
- 文字数・行数・画像枚数を**数値で固定**すれば、機械チェック可
- 違反検出 → 自動修正のループが回せる
- 今回も初回 5/14 違反 → 自動短縮で 100% 準拠

→ 制約の明文化は AI への指示の品質を根本から上げる。

### 4.2 デザインシステム思想を持ち込む

- 「テーマ・実装例・ルール・ジェネレータ」の 4 点セット
- React や Figma で当たり前のことを、スライドにも適用する
- 同じレイアウトを 2 回使ったらコンポーネント化、を運用ルールに

### 4.3 Single Source of Truth を意識する

- スキーマ・規約・ルールが 3 箇所に書いてあると 100% ズレる
- 1 箇所に集約してリンクで参照させる(案B)
- 「テンプレを直すだけで全 Skill が追従」状態を作る

### 4.4 入出力をテキストで完結させる

- バイナリやドラッグ操作を経由しない
- input.md → output/*.md → PDF というテキスト変換チェーン
- 途中の状態がすべて Read 可能なので、Claude が自己修正できる

### 4.5 スモークテストを早く回す

- テンプレを作ったら**すぐサンプルで動かす**
- 動かしてみないと「.comparison-matrix の width が効かない」みたいな細かい問題は見えない
- ドッグフーディングが品質保証の最短経路

---

## 5. このアーキテクチャの応用可能性

「AI に Markdown を書かせれば自動化できる」のは、スライドだけの話ではない。

| 用途 | フォーマット | ツール |
|---|---|---|
| スライド | Markdown | **Marp** ← 今回 |
| ブログ記事 | Markdown | Zenn, Qiita, GitHub Pages |
| API 仕様書 | Markdown / YAML | OpenAPI, Swagger |
| アーキ図 | テキスト DSL | Mermaid, PlantUML, D2 |
| ER 図 | テキスト DSL | Mermaid, DBML |
| インフラ | HCL / YAML | Terraform, Kubernetes |
| UI コンポーネント | JSX / Vue | React, Vue |
| 数式 | LaTeX | Pandoc, KaTeX |

すべて「**テキスト → ビジュアル**」変換が定義されているので、AI に任せやすい。
逆に「ビジュアル → ビジュアル」のフォーマットは AI が苦戦する(Photoshop, Figma の細部操作など)。

→ ツール選定時に「**この形式は AI が書けるか?**」を必ず考慮する価値がある。

---

## 6. 今後の発展余地(memo)

- Slidev 派生(`slide-slidev/`)を別ディレクトリで構築 → Vue/Tailwind 学習
- Puppeteer MCP セットアップ + 自動ビジュアルレビューループ
- 画像生成 AI と連携(`images/` を自動で埋める)
- 会社ブランド対応(`themes/theme.css` の `--color-primary` 書き換えのみで完結)
- `Claude Code Action` 経由で GitHub から `@claude` でスライド生成
- `slide-similarity` Skill(過去スライドとの類似度チェックで自己剽窃を防ぐ)

---

## 7. 参考文献

- [Marp 公式](https://marp.app/)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [cursor-to-marp by Kumaiu](https://github.com/Kumaiu/cursor-to-marp)
- [登壇スライド作成も Claude Code に任せたい(Zenn, loglass kagaya)](https://zenn.dev/loglass/articles/a8b4b069c09002)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Anthropic - Claude Code Slash Commands](https://docs.anthropic.com/en/docs/claude-code/slash-commands)
- [Slidev 公式](https://sli.dev/)(派生候補)

---

> 最後に: このテンプレが誰かの登壇準備の苦痛を和らげますように。
> そして、登壇内容を考えることに集中できますように。
