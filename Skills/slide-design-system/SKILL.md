---
name: slide-design-system
description: themes/theme.css の編集とコンポーネント追加。スライドのデザインシステムを育てる
---

# slide-design-system — デザインシステム保守

## トリガーワード

- 「テーマを直して」「スタイルを変えて」
- 「コンポーネントを追加」「クラスを増やして」
- 「色を変えて」「ブランドカラーを変えて」
- 「CSS を整えて」「スライドの見た目を改善」

## 命名規約

- **コンポーネントは BEM 風** または `.{name}` シンプル名
  - 例: `.metric-card`, `.metric-card .metric-number`, `.metric-card small`
- **修飾子はドット連結**: `.badge.primary`, `.highlight-box.warning`
- **特殊ページは section クラス**: `section.cover`, `section.divider`, `section.end`

## 既存コンポーネント一覧

| クラス | 用途 |
|---|---|
| `section.cover` | 表紙 |
| `section.divider` | セクション区切り |
| `section.end` | エンドスライド |
| `.columns-2` / `.columns-3` | グリッドカラム |
| `.metric-card` | KPI 数値強調 |
| `.strategy-grid` / `.strategy-section` | 戦略 2x2 マトリクス |
| `.comparison-matrix` | 比較表 |
| `.highlight-box` (`.warning` / `.danger` / `.success`) | 強調枠 |
| `.feature-cards` / `.feature-card` | 機能カード |
| `.roadmap` / `.roadmap-step` | ロードマップ |
| `.testimonial` | 引用ブロック |
| `.case-study` | 事例紹介 |
| `.badge` (`.primary` / `.success` / `.warning` / `.danger`) | ラベル |
| `.tech-stack` / `.tech` | 技術スタック一覧 |

## 編集手順

### 1. 既存コンポーネントの修正

1. `themes/theme.css` を Read
2. 該当クラスを Edit
3. `templates/YYYYMMDD_template.md` の利用箇所に影響がないか確認
4. 既存の `output/*.md` で使われていたら、PDF を再生成して目視確認

### 2. 新規コンポーネントの追加

1. `themes/theme.css` の末尾に `/* Component: .{name}(用途) */` で追加
2. `templates/YYYYMMDD_template.md` に**使用例を必ず追加**(Storybook 同等)
3. 本ファイル(SKILL.md)の「既存コンポーネント一覧」に追記
4. `themes/theme.css` の `:root` トークンを使い、ベタな色は書かない

### 3. ブランドカラー変更

1. `themes/theme.css` の `:root` 内の `--color-primary`, `--color-accent` を Edit
2. 影響する全コンポーネントが追従するか確認
3. 既存スライドの PDF を再生成して色が反映されているか確認

## 守ること

- **トークン経由**: 直接 `#0a2e5b` などを書かず、`var(--color-primary)` を使う
- **責務分離**: コンポーネント間の依存を作らない(`.foo .bar` の長い連鎖は避ける)
- **モバイル考慮しない**: スライドはフルスクリーン固定
- **!important 禁止**: どうしても必要な箇所(`::before` の z-index など)以外は使わない
- **コメントで何のためのものか書く**: `/* Component: .xxx(用途) */`

## エスカレーション

- ブランドガイドに直接関わる変更(ロゴ画像差し替え、フォント変更)はユーザに確認
- 既存 30 件以上のスライドで使われているクラス名のリネーム/削除はユーザに確認
