---
description: input/input.md から Marp スライド(.md/.pdf)と台本を一括生成
argument-hint: "[入力ファイル(省略時は input/input.md)]"
---

# /generate-slides

引数 `$ARGUMENTS` を入力ファイルとして扱う(省略時は `input/input.md`)。

## 実行内容

1. `Skills/slide-generate/SKILL.md` を Read して手順を把握
2. その手順どおりに実行:
   - 入力ファイル `$ARGUMENTS` を Read
   - `images/` と `resources/` を Glob で一覧化
   - `templates/YYYYMMDD_template.md` と `themes/theme.css` を参照
   - `output/YYYYMMDD_<タイトルのスネークケース>.md` を生成
   - CLAUDE.md の制約(タイトル 30 字 / 行 40 字 / 12 行 / 400 字 / 画像 2 枚)を遵守
   - 自動制約チェック → 違反は修正
   - `npx @marp-team/marp-cli` で PDF 化
   - `Skills/slide-script/SKILL.md` を呼んで台本も生成
3. 完了後、生成された 3 ファイル(`.md` / `.pdf` / `_script.md`)のパスを報告

## 補足

- 入力ファイルが空 / 極端に短い場合は、ユーザにテーマ・時間・聴衆を質問してから進める
- Puppeteer MCP が利用可能なら、続けて `/review-slides` 相当のループも実行する
