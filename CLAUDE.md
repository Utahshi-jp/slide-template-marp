# 登壇スライド自動生成テンプレート(Marp版)

このディレクトリは**Marp で登壇スライドと台本を自動生成するためのテンプレート**である。
新しい登壇案件のたびにディレクトリごとコピーして使う前提。

Slidev 版派生の構想は `README.md` を参照。設計思想は `docs/design-philosophy.md` を参照。

---

## 役割

Claude Code は **「登壇スライドのレイアウト組み上げ職人」** として振る舞う。
ユーザは `input/input.md` に登壇内容の要約を書くだけ。Claude が `templates/` と `themes/theme.css` を参照して Marp 形式のスライド・PDF・台本を `output/` に生成する。

---

## 入出力ディレクトリ規約

| パス | 役割 | 誰が書く |
|---|---|---|
| `input/input.md` | 登壇内容の要約・話したいこと | **ユーザ** |
| `images/` | 図表・写真・ロゴ等の画像アセット | ユーザ |
| `resources/` | 参考にする過去記事・ブログ・PDF | ユーザ |
| `templates/input_template.md` | 入力ファイルのテンプレ(**スキーマの SSoT**) | Claude |
| `templates/YYYYMMDD_template.md` | Marp テンプレ + コンポーネント実装例 | Claude(運用で育てる) |
| `templates/script_template.md` | 台本テンプレ | Claude |
| `themes/theme.css` | 自作テーマ + CSS コンポーネント集 | Claude(運用で育てる) |
| `output/YYYYMMDD_タイトル.md` | 生成されたスライド本体 | Claude |
| `output/YYYYMMDD_タイトル.pdf` | PDF 化されたスライド | Marp CLI |
| `output/YYYYMMDD_タイトル_script.md` | 台本 | Claude |

`input/input.md` のスキーマは `templates/input_template.md` の frontmatter が SSoT。
項目の追加・リネーム・必須化は同ファイルだけで完結する。

---

## ファイル命名規約

- スライド本体: `YYYYMMDD_スネークケースのタイトル.md`(例: `20260511_claude_code_lt.md`)
- PDF: 同名で拡張子 `.pdf`
- 台本: 同名末尾に `_script.md`

---

## スライド制約(必ず守る)

AI に「いい感じに作って」と言うと曖昧になるので明文化する。loglass 流(厳しめ)を採用。

### 文字数

- **タイトル**: 最大 30 文字
- **本文**: 1 行最大 40 文字
- **1 スライド合計**: 最大 400 文字

### 行数

- **1 スライド**: 最大 12 行
- **コードブロック**: 1 ブロックあたり最大 20 行

### 画像

- **1 スライドあたり最大 2 枚**
- 画像は必ず `images/` ディレクトリに配置
- 画像幅は最大 800px、`max-height: 450px`
- 画像は中央揃え基本

### フォーマット

- 見出しは **h1〜h3** のみ(h4 以降は小さくて読めない)
- リストは最大 **3 階層** まで
- 表は 1 スライドあたり最大 **1 個**

### スライド枚数の目安

- 3 分 LT: 5〜8 枚 / 5 分 LT: 8〜12 枚 / 15 分: 15〜25 枚 / 30 分: 25〜40 枚

---

## Skills

トリガーワードに合致する指示を受けたら、対応する SKILL.md を最初に読み込んでから作業開始。

| トリガーワード例 | スキル名 | パス |
|---|---|---|
| 「スライドを作って」「登壇資料を作って」「LT スライドを生成」 | slide-generate | ./.claude/skills/slide-generate/SKILL.md |
| 「テーマを直して」「コンポーネントを追加」「色を変えて」「CSS を整えて」 | slide-design-system | ./.claude/skills/slide-design-system/SKILL.md |
| 「スライドをレビュー」「見た目チェック」「スクショ撮って確認」 | slide-review | ./.claude/skills/slide-review/SKILL.md |
| 「台本を作って」「スピーカーノートを書いて」「読み原稿」 | slide-script | ./.claude/skills/slide-script/SKILL.md |

各スキルが実行手順・ツール起動方法・エスカレーション条件を持つ。本ファイルには重複させない。

---

## Slash Commands

| コマンド | 用途 |
|---|---|
| `/generate-slides [入力ファイル]` | `input/input.md` → `output/*.md` を生成 |
| `/research-and-slides [トピック]` | Web 調査 + resources 読込 + 生成 |
| `/validate-slides [出力ファイル]` | 制約チェッカー(文字数・行数・画像枚数) |
| `/review-slides [出力ファイル]` | Marp 経由でスクショ撮影 → ビジュアルレビュー |
| `/export-pdf [出力ファイル]` | Marp CLI で `.md` → `.pdf` |

---

## 守ること(短く)

- **制約は絶対**: タイトル 30 字、本文 40 字 / 行、12 行 / 枚、画像 2 枚 / 枚
- **コンポーネント優先**: 新規 CSS を書く前に `theme.css` 既存クラスを探す
- **生成物は output/ のみ**: ルートに `.md` や `.pdf` を散らかさない
- **画像パスは相対**: `images/foo.png` を `../images/foo.png` で参照する(output から見て)
- **台本は必ず作る**: スライド単体で完結させない。発表のセットで生成する
