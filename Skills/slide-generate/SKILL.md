---
name: slide-generate
description: input/input.md を Marp スライド(.md/.pdf)と台本に変換するメインスキル
---

# slide-generate — 登壇スライド自動生成

## トリガーワード

- 「スライドを作って」「スライド作成」
- 「登壇資料を作って」
- 「LT スライドを生成」「セッション資料を生成」
- 「`input.md` をスライド化して」

## 実行手順

### Step 1: 入力の確認

1. **スキーマ(SSoT)を読み込む**:
   - `templates/input_template.md` を Read
   - frontmatter のコメント `# --- 必須 ---` と `# --- 任意 ---` で必須/任意項目を判定
   - 本ファイルにスキーマを重複定義しない。常にテンプレを正とする
2. **入力を読む**:
   - `input/input.md` を Read
   - frontmatter(`---` で囲まれた YAML)をパースし、上記スキーマと突き合わせる
   - `schema_version` がテンプレと不一致なら、ユーザに警告(マイグレーション提案)
3. **欠落チェック**:
   - 必須項目が空 / 欠落していたら、不足項目を列挙して `templates/input_template.md` の該当箇所を案内し停止
   - frontmatter 自体が無ければ、テンプレからのコピーを促す
4. **本文(frontmatter 以降)**は自由記述として扱う:
   - `## 話したいこと` / `## 数字データ` / `## 構成案` / `## 画像メモ` / `## 補足メモ` の見出しがあれば優先的に拾う
5. **アセット一覧**:
   - `images/` 配下を `Glob` で一覧化(`images/**/*.{png,jpg,jpeg,svg,gif}`)
   - `resources/` 配下を `Glob` で一覧化(`resources/**/*.{md,pdf,html}`)
   - 関連が深そうなら `resources/` の Markdown は中身を Read で取り込む

> **重要**: スキーマの定義はこの Skill ファイル内には書かない。必ず `templates/input_template.md` の frontmatter を Single Source of Truth として参照する。

### Step 2: 構成設計

枚数の目安は CLAUDE.md の制約を参照。
基本テンプレ:

1. **表紙**(`<!-- _class: cover -->`)
2. **アジェンダ**
3. 各章の**区切り**(`<!-- _class: divider -->`)
4. **本文スライド群**
5. **まとめ**
6. **エンド**(`<!-- _class: end -->`)

各本文スライドには 1 メッセージのみ載せる。詰め込まない。

### Step 3: 生成

1. `templates/YYYYMMDD_template.md` を Read してコンポーネントの使い方を把握
2. `themes/theme.css` を Read してクラス名一覧を把握
3. `output/YYYYMMDD_スネークケースのタイトル.md` を Write
   - 日付は今日(`YYYYMMDD`)
   - frontmatter は templates と同じ形式を維持
   - 画像参照は `../images/foo.png`(output から見た相対パス)

### Step 4: 制約チェック

以下を**全スライドで**満たすこと:

- タイトル ≤ 30 字
- 本文 1 行 ≤ 40 字
- 1 スライド合計 ≤ 400 字
- 1 スライド ≤ 12 行
- 1 スライド画像 ≤ 2 枚
- 見出し h1〜h3 のみ
- 表は 1 スライド 1 個まで
- コードブロックは 20 行まで

違反があれば**該当スライドを 2 枚に分割するか、文章を短縮**して修正する。

### Step 5: PDF 出力

Bash で:

```bash
npx --yes @marp-team/marp-cli@latest "output/YYYYMMDD_xxx.md" \
  --pdf --allow-local-files --theme themes/theme.css
```

失敗時の対処:

- フォント関連: `themes/theme.css` の `font-family` を Hiragino Sans / Noto Sans CJK JP に固定
- 画像が出ない: パスを `../images/` に再確認 / `--allow-local-files` 必須
- テーマが反映されない: `--theme` の指定漏れ、または frontmatter の `theme:` が不一致

### Step 6: 台本生成

`slide-script` Skill を呼んで `output/YYYYMMDD_xxx_script.md` を生成。

### Step 7: ビジュアルレビュー(可能なら)

Puppeteer MCP が利用可能なら `slide-review` Skill を呼んで自動レビューループ(最大 3 周)。
利用不可なら省略し、ユーザに「VS Code の Marp プレビューで確認してください」と案内。

### Step 8: 完了報告

ユーザに以下のパスを提示する:

- `output/YYYYMMDD_xxx.md`(スライド本体)
- `output/YYYYMMDD_xxx.pdf`(配布用 PDF)
- `output/YYYYMMDD_xxx_script.md`(台本)

## 守ること

- **コンポーネント優先**: 自由なレイアウトより `theme.css` のクラスを使う
- **再現性**: 「いい感じに」を避け、必ずクラス名で構成
- **制約絶対**: 制約違反 0 件を目指す
- **タイトル削り**: 30 字を超えたら、副題に逃がす(`## サブタイトル`)
- **数字は強調**: 重要な数字は `.metric-card` で必ず可視化

## エスカレーション条件

以下に該当する場合は作業を止めてユーザに確認する:

- `themes/theme.css` の大幅改修(ブランドカラー変更、ロゴ位置変更など)
- 既存のコンポーネントクラス名を変更/削除する場合
- 会社/プロダクトのブランドガイドからの逸脱が必要な場合
- ユーザ提供の `images/` に該当ファイルが見つからず代替が必要な場合
- 登壇枚数が想定の 2 倍を超えそうな場合

## 出力フォーマット例

```markdown
---
marp: true
theme: custom
paginate: true
footer: 'Copyright (C) YourCompany, All Rights Reserved.'
---

<!-- _class: cover -->

# {タイトル(30 字以内)}

## {サブタイトル}

**{YYYY.MM.DD} / {登壇者} / {所属}**

---

# 本日のアジェンダ

1. {1 章}
2. {2 章}
3. {3 章}
4. まとめ
```
