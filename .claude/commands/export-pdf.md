---
description: Marp CLI で .md → .pdf を出力する
argument-hint: "[出力ファイル(省略時は output/ 配下の最新)]"
---

# /export-pdf

引数で指定された Marp Markdown を PDF 化する。

## 実行内容

1. `$ARGUMENTS`(省略時は `output/` 配下で最新の `*.md`)を確認
2. 以下を Bash で実行:

```bash
npx --yes @marp-team/marp-cli@latest "<入力ファイル>" \
  --pdf --allow-local-files --theme themes/theme.css
```

3. 生成された PDF のパスを報告

## 失敗時の対処

| 症状 | 対処 |
|---|---|
| 日本語フォントが豆腐になる | `themes/theme.css` の `font-family` を Hiragino Sans / Noto Sans CJK JP に固定 |
| 画像が表示されない | `--allow-local-files` の指定漏れ、画像パスを再確認 |
| テーマが効かない | `--theme themes/theme.css` を明示、frontmatter の `theme:` 一致確認 |
| Chromium 取得で固まる | `PUPPETEER_SKIP_DOWNLOAD=1` で別途 Chrome を使うよう設定 |

## 補足

- PDF だけでなく `--html` や `--images png` も同じ仕組みで出せる
- 連続出力(`output/*.md` 全部)は `for` ループで処理可
