---
description: HTML 生成 + Puppeteer MCP スクショ → ビジュアル評価 → 自動修正(最大 3 周)
argument-hint: "[出力ファイル(省略時は output/ 配下の最新)]"
---

# /review-slides

スライドをブラウザレンダリングしてスクショを撮り、Claude がビジュアル評価して問題を直す。

## 実行内容

1. `Skills/slide-review/SKILL.md` を Read して手順を把握
2. 手順に従って以下を順に実行:
   - Marp CLI で HTML / PNG を出力
   - Puppeteer MCP でページごとにスクショ(MCP がない場合は PNG を Read で読み込み)
   - 各ページのチェックリスト(はみ出し / 重なり / ロゴ / コントラスト / 余白)を評価
   - 問題があれば該当スライド Markdown を Edit で修正
3. **最大 3 周** ループ。改善が止まったら早めに打ち切る
4. 完了報告:
   - 修正したスライド番号と修正内容のサマリ
   - 残課題があれば箇条書きで提示

## 補足

- Puppeteer MCP が未設定なら、`Marp CLI --images png` で出力した PNG を画像認識で評価
- それも難しい場合は「VS Code Marp プレビューで手動確認してください」とフォールバック
