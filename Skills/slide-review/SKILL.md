---
name: slide-review
description: Marp で HTML/PNG 出力し、Puppeteer MCP でスクショ撮影 → ビジュアルレビュー → 修正の自動ループ
---

# slide-review — ビジュアルレビュー & 自動修正ループ

## トリガーワード

- 「スライドをレビュー」「見た目チェック」
- 「スクショを撮って確認」
- 「ビジュアル確認」「レイアウト確認」

## 前提

- Puppeteer MCP が `.mcp.json` に登録されている
- Marp CLI が `npx` で実行可能

未セットアップなら、本スキルはフォールバックして「VS Code Marp プレビューで手動確認してください」と案内する。

## 実行手順

### Step 1: HTML / PNG 出力

```bash
# HTML 出力
npx --yes @marp-team/marp-cli@latest "output/YYYYMMDD_xxx.md" \
  --html --allow-local-files --theme themes/theme.css

# 全ページ PNG 出力
npx --yes @marp-team/marp-cli@latest "output/YYYYMMDD_xxx.md" \
  --images png --allow-local-files --theme themes/theme.css \
  -o "output/preview/page.png"
```

### Step 2: スクショ確認(Puppeteer MCP)

Puppeteer MCP で生成 HTML をブラウザレンダ → ページごとにスクショ:

```
puppeteer__navigate({ url: "file:///.../output/YYYYMMDD_xxx.html" })
puppeteer__screenshot({ name: "slide_01", selector: "section:nth-of-type(1)", width: 1280, height: 720 })
```

または Marp CLI の `--images png` 出力 PNG を Read で取り込んで Claude 自身が画像認識する。

### Step 3: ビジュアル評価チェックリスト

各スライドについて以下を確認:

- [ ] **はみ出し**: テキストが画面外に出ていないか
- [ ] **重なり**: 要素が他の要素と被っていないか
- [ ] **ロゴ位置**: 右上のロゴが消えていないか(`section:not(.cover):not(.divider):not(.end)::before`)
- [ ] **コントラスト**: 背景と文字の色差は十分か
- [ ] **画像サイズ**: 画像が極端に大きい / 小さい / つぶれていないか
- [ ] **余白**: 詰め込みすぎていないか
- [ ] **タイトル**: 1 行に収まっているか
- [ ] **コード**: シンタックスハイライトが崩れていないか
- [ ] **アジェンダ整合**: 全体構成と各章タイトルが一致しているか

### Step 4: 修正

問題があれば該当スライドの Markdown を Edit して修正。代表的な対処:

| 症状 | 対処 |
|---|---|
| 文字はみ出し | 文章を短縮、または 2 スライドに分割 |
| ロゴ消失 | `<!-- _class: ... -->` を確認、`images/logo.png` の有無を確認 |
| 画像でかすぎ | `style="max-width: 600px;"` を画像タグに付加 |
| コード折返し | 短いコードに差し替え、または `<pre><code>` でラップ |
| テーブル崩れ | 列幅を CSS で固定 / 表のセル数を減らす |

修正後は Step 1 から再実行。

### Step 5: ループ制御

- **最大 3 周**で打ち切る(無限ループ防止)
- 3 周しても解消しない問題は、ユーザに状況とスクショを共有して相談
- 改善が見られない場合は早めに打ち切り、原因切り分け(CSS or Markdown どちら)を提案

## 守ること

- **3 周ルール厳守**
- **修正前後の差分を 1 行で記録**(将来テーマ改善のヒントにする)
- **無理な修正をしない**: コンテンツの中身まで書き換えない、レイアウトの範囲で対処
