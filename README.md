# Marp スライド自動生成テンプレート

Claude Code で **登壇スライドと台本を自動生成** するためのテンプレート。
新しい登壇案件のたびに、このディレクトリを丸ごとコピーして使う。

参考にしたのは下記 2 つ:

- [Kumaiu/cursor-to-marp](https://github.com/Kumaiu/cursor-to-marp) — Cursor + Marp の最小構成
- [登壇スライド作成も Claude Code に任せたい](https://zenn.dev/loglass/articles/a8b4b069c09002) — loglass / kagaya 氏のデザインシステム思想

これらの良いとこ取りで、Claude Code ネイティブのワークフローにしてある。

> **設計の経緯と「なぜ Marp × AI でスライドが自動化できるのか」の概念整理** → [`docs/journey.md`](./docs/journey.md)

---

## クイックスタート

```bash
# 1. 入力を書く
編集: input/input.md

# 2. 画像と参考資料を配置(任意)
images/         に画像(.png/.jpg/.svg)
resources/      に過去記事やブログの .md/.pdf

# 3. Claude Code で生成コマンドを叩く
/generate-slides
```

出力は `output/` に 3 ファイル:

- `YYYYMMDD_タイトル.md`(Marp スライド本体)
- `YYYYMMDD_タイトル.pdf`(配布用 PDF)
- `YYYYMMDD_タイトル_script.md`(台本)

---

## ディレクトリ構成

```
slide/
├── CLAUDE.md                         # Claude Code への指示(制約・Skill 一覧)
├── README.md                         # このファイル
├── .claude/
│   └── commands/                     # slash command 群
│       ├── generate-slides.md        # input.md → output/.md 生成
│       ├── research-and-slides.md    # Web 調査 + 生成
│       ├── validate-slides.md        # 制約チェック
│       ├── review-slides.md          # スクショ → ビジュアルレビュー
│       └── export-pdf.md             # Marp CLI で PDF 化
├── Skills/
│   ├── slide-generate/SKILL.md       # スライド生成本体
│   ├── slide-design-system/SKILL.md  # theme.css / コンポーネント追加
│   ├── slide-review/SKILL.md         # ビジュアルレビュー
│   └── slide-script/SKILL.md         # 台本生成
├── themes/
│   └── theme.css                     # 自作テーマ + コンポーネント集
├── templates/
│   ├── input_template.md             # 入力スキーマ(SSoT)
│   ├── YYYYMMDD_template.md          # Marp テンプレ(Storybook 相当)
│   └── script_template.md            # 台本テンプレ
├── input/
│   └── input.md                      # 登壇要約(ユーザが書く)
├── images/                           # 画像アセット(.png/.jpg/.svg)
├── resources/                        # 参考資料(.md/.pdf)
├── output/                           # 生成物の置き場
└── docs/                             # 設計思想・経緯メモ(design-philosophy.md / journey.md)
```

---

## セットアップ

### 1. 必須

- **Node.js** 18 以上(`npx` を使うため)
- **Claude Code** インストール済み

### 2. 推奨

- **VS Code 拡張 [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)**
  - エディタ上でリアルタイムプレビュー
  - 設定で `markdown.marp.themes` に `./themes/theme.css` を追加

```json
{
  "markdown.marp.themes": ["./themes/theme.css"]
}
```

### 3. Puppeteer MCP(ビジュアルレビュー用、任意)

`.mcp.json` または `~/.claude/settings.json` の `mcpServers` に追加:

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

未設定でも基本フローは動く(`slide-review` が手動確認モードにフォールバック)。

### 4. ブランドアセット

- `images/logo.png` を置くと、`section:not(.cover):not(.divider):not(.end)::before` で全スライド右上に自動表示
- ブランドカラーを変えるときは `themes/theme.css` の `:root --color-primary` / `--color-accent` を編集

---

## 使い方

### 基本フロー

1. `input/input.md` に登壇内容の要約を書く
   - メタ情報(タイトル / 想定時間 / 想定聴衆)
   - 話したいこと(箇条書きで OK)
   - 数字データ
   - 任意で構成案
2. 画像があれば `images/`、参考資料は `resources/` に置く
3. Claude Code を起動して `/generate-slides` を実行
4. 完了後、`output/` に 3 ファイルが生成される
5. 必要なら `/review-slides` でビジュアル確認、`/validate-slides` で制約再チェック

### Web 調査からまとめてやる

```
/research-and-slides EPM 市場の最新トレンド
```

- Web 調査 → input/input.md 生成 → スライド生成、を一気通貫で実行

### テーマを育てる

「`.metric-card` の色を変えて」「新しいコンポーネント `.timeline` を追加して」など、自然言語で `theme.css` を編集できる。

```
Claude Code: テーマに「タイムライン用のコンポーネント」を追加して
→ Skills/slide-design-system が起動 → themes/theme.css と templates に追記
```

---

## スライド制約(必ず守る)

| 項目 | 制限 |
|---|---|
| タイトル文字数 | ≤ 30 字 |
| 本文 1 行 | ≤ 40 字 |
| 1 スライド合計 | ≤ 400 字 |
| 1 スライド行数 | ≤ 12 行 |
| コードブロック | ≤ 20 行 |
| 画像枚数 / 枚 | ≤ 2 枚 |
| 表 / 枚 | ≤ 1 個 |
| 見出し | h1〜h3 |
| リスト階層 | ≤ 3 |

数値の最終決定権は `CLAUDE.md` にある。設計思想は `docs/design-philosophy.md` 参照。

---

## デザインシステム思想

「4 点セット(`theme.css` / `CLAUDE.md` / `templates/` / `Skills/`)で品質を担保する」という設計を採用している。
詳細は [`docs/design-philosophy.md`](./docs/design-philosophy.md) 参照。

運用上の鉄則: **同じレイアウトを 2 回以上使ったら `theme.css` にコンポーネント化する**。

---

## トラブルシューティング

| 症状 | 対処 |
|---|---|
| 日本語が豆腐になる | `themes/theme.css` の `font-family` を確認(Hiragino Sans / Noto Sans CJK JP) |
| 画像が表示されない | `--allow-local-files` の指定漏れ、パスを `../images/foo.png` に |
| テーマが効かない | `--theme themes/theme.css` を明示、frontmatter `theme: custom` も確認 |
| ロゴが右上に出ない | `images/logo.png` を配置したか確認 |
| Puppeteer が落ちる | `PUPPETEER_SKIP_DOWNLOAD=1` で既存 Chrome を使う |

---

## Slidev への展開メモ

このテンプレは **Marp 専用** だが、同じ `input/input.md` を入力に Slidev 版を派生させる構想もある。Slidev は **Vue 3 + Vite + UnoCSS** で動くため、インタラクティブ要素やフロントエンド学習を兼ねたい場合に有用。

入力ファイルだけ共有し、Slidev 派生はリポジトリを分けて構築する想定。同じ内容を 2 フォーマットで生成できる利点がある一方、Marp ほど AI 生成と相性は良くないため、用途に応じて選ぶ。

---

## 参考リンク

- [Marp 公式](https://marp.app/)
- [Marp for VS Code](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)
- [Marp CLI](https://github.com/marp-team/marp-cli)
- [Slidev 公式](https://sli.dev/)
- [Claude Code ドキュメント](https://docs.anthropic.com/claude-code)
- [Puppeteer MCP](https://github.com/modelcontextprotocol/servers/tree/main/src/puppeteer)
