---
marp: true
theme: custom
paginate: true
footer: 'Copyright (C) YourCompany, All Rights Reserved.'
style: |
  /* スライド単位の追加 CSS が必要ならここに */
---

<!-- _class: cover -->

# プレゼンテーションのタイトル

## サブタイトルを入れる

**YYYY.MM.DD / 登壇者名 / 所属**

---

# 本日のアジェンダ

1. **背景と課題**
2. **解決アプローチ**
3. **実装と効果**
4. **まとめと今後**

---

<!-- _class: divider -->

# 1. 背景と課題

---

# なぜ今この話をするのか

### 一言で

- 既存のやり方では限界に直面
- 新しい選択肢が登場し、現場が変わり始めた
- 自分たちの取り組みを共有したい

<div class="highlight-box">
本セッションのゴール: 聴衆が「明日試したい」と思えるレベルまで具体に落とす
</div>

---

# 現状の数字で見る課題

<div class="columns-3">
  <div class="metric-card">
    <div class="metric-number">73<small>%</small></div>
    <p>手作業に費やす時間の割合</p>
  </div>
  <div class="metric-card">
    <div class="metric-number">5.2<small>h</small></div>
    <p>1 件あたりの平均作業時間</p>
  </div>
  <div class="metric-card">
    <div class="metric-number">2.4<small>x</small></div>
    <p>過去 1 年での増加倍率</p>
  </div>
</div>

---

<!-- _class: divider -->

# 2. 解決アプローチ

---

# 全体像

<div class="strategy-grid">
  <div class="strategy-section">
    <h3>💡 アイデアの整理</h3>
    <p>AI を使った発想支援</p>
    <span class="badge primary">起点</span>
  </div>
  <div class="strategy-section">
    <h3>⚙️ 自動化レイヤー</h3>
    <p>定型作業をパイプライン化</p>
    <span class="badge success">本丸</span>
  </div>
  <div class="strategy-section">
    <h3>📊 可視化と計測</h3>
    <p>効果を数値で確認</p>
    <span class="badge warning">継続</span>
  </div>
  <div class="strategy-section">
    <h3>🔁 フィードバック</h3>
    <p>運用しながら改善</p>
    <span class="badge primary">改善</span>
  </div>
</div>

---

# 既存手法との比較

<table class="comparison-matrix">
  <thead>
    <tr><th>観点</th><th>従来手法</th><th>新アプローチ</th></tr>
  </thead>
  <tbody>
    <tr><td>所要時間</td><td>5.2h / 件</td><td>0.8h / 件</td></tr>
    <tr><td>属人性</td><td>高い</td><td>低い</td></tr>
    <tr><td>初期投資</td><td>低い</td><td>中</td></tr>
    <tr><td>長期 ROI</td><td>低い</td><td>高い</td></tr>
  </tbody>
</table>

---

<!-- _class: divider -->

# 3. 実装と効果

---

# 進め方(3 ステップ)

<div class="roadmap">
  <div class="roadmap-step">
    <div class="step-no">01</div>
    <h3>PoC</h3>
    <p>狭い範囲で 2 週間</p>
  </div>
  <div class="roadmap-step">
    <div class="step-no">02</div>
    <h3>パイロット</h3>
    <p>1 チームで 1 か月</p>
  </div>
  <div class="roadmap-step">
    <div class="step-no">03</div>
    <h3>全社展開</h3>
    <p>3 か月で段階展開</p>
  </div>
</div>

---

# 使った技術

<div class="tech-stack">
  <span class="tech">🟦 TypeScript</span>
  <span class="tech">⚛️ Next.js</span>
  <span class="tech">🤖 Claude Code</span>
  <span class="tech">🟢 Node.js</span>
  <span class="tech">🐍 Python</span>
  <span class="tech">🐳 Docker</span>
</div>

---

# 機能カードで紹介

<div class="feature-cards">
  <div class="feature-card">
    <h3>🔍 自動検索</h3>
    <p>キーワード抽出から候補抽出までを 1 操作で完結</p>
  </div>
  <div class="feature-card">
    <h3>📝 自動下書き</h3>
    <p>テンプレに沿った下書きを生成、人間は校正のみ</p>
  </div>
  <div class="feature-card">
    <h3>🔔 通知連携</h3>
    <p>完了時に Slack や Teams にスレッド投稿</p>
  </div>
</div>

---

# お客様の声

<div class="testimonial">
  作業時間が 1/5 になっただけでなく、若手の心理的負担が大きく下がりました。
  検討の質が確実に上がっています。
  <span class="author">― 利用部門マネージャ</span>
</div>

---

# 導入事例

<div class="case-study">
  <h3>📦 案件 A: 受発注業務の自動化</h3>
  <div class="meta">業種: 製造業 / 規模: 月 300 件 / 期間: 3 か月</div>
  <p>夜間バッチ化と Slack 通知の組み合わせで、翌朝の確認業務が 80% 削減。</p>
  <span class="badge success">効率化</span>
  <span class="badge primary">UX 改善</span>
</div>

---

# サンプルコード

```typescript
// 自動下書きのコア部分(抜粋)
export async function draft(input: Input): Promise<Output> {
  const intent = await classifyIntent(input.text);
  const template = pickTemplate(intent);
  const draft = await fillTemplate(template, input);
  return { intent, draft, suggestions: [] };
}
```

---

# 効果のまとめ

<div class="highlight-box success">
- 作業時間: 5.2h → 0.8h(85% 削減)
- 品質: レビュー差し戻し率 40% → 12%
- 心理的安全: 「やらされ感」のアンケートスコアが大幅改善
</div>

---

<!-- _class: divider -->

# 4. まとめと今後

---

# 今日のテイクアウェイ

1. **小さく始める**: PoC は狭い範囲で 2 週間
2. **計測する**: 時間・品質・心理の 3 軸を継続観測
3. **コンポーネント化**: 同じパターンは資産にして増殖させる

<div class="highlight-box">
完璧な設計より、回せる仕組みを優先する。改善は運用しながら。
</div>

---

# 参考リンク

- Marp 公式: <https://marp.app/>
- Claude Code ドキュメント: <https://docs.anthropic.com/claude-code>
- 当日の登壇資料・台本一式は GitHub にて公開

---

<!-- _class: end -->

# Thank You !

ご質問は SNS / 懇親会で気軽にどうぞ
