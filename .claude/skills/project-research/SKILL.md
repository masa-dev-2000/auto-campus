---
name: project-research
description: /new-projectの後に実行。競合分析・市場調査を行いSTATE.mdを更新する。新プロジェクトの競合調査フェーズ専用。
when_to_use: /new-projectのヒアリング完了後に競合を調べたい、プロジェクトの市場調査をしたい
allowed-tools: Read Write Bash(mkdir *) Bash(git *) WebSearch WebFetch
argument-hint: "[--yolo で確認なし実行]"
effort: medium
context: fork
---

# /project-research — 競合・市場リサーチ（プロジェクト開始フロー Phase 2）

## 前提コンテキスト（自動取得）

!`cat docs/project/STATE.md 2>/dev/null || echo "STATE.md が見つかりません。先に /new-project を実行してください。"`

---

## 事前確認

STATE.md が存在しない場合: **「先に `/new-project` を実行してください」と伝えて終了。**

STATE.md の「一言サマリー」を読み込んで、何を作るプロジェクトかを把握してから進む。

---

## STEP 1: リサーチ実行

`WebSearch` と `WebFetch` で以下を調査（最低5情報源）:

**調査項目:**
- このアイデアと同じ問題を解決する既存ツール（上位5件）
- 各ツールの「できること」「できないこと」
- ユーザーレビュー・フォーラムでの不満点（Reddit・Product Hunt・G2・Hacker News）

**検索キーワードの工夫:**
- 競合発見: `[カテゴリ名] alternatives` / `best [カテゴリ] tools 2025`
- ユーザー不満: `[ツール名] reddit complaints` / `[ツール名] missing features`
- 市場規模: `[カテゴリ] market size 2025`

**情報品質ルール:**
- 数値（価格・ユーザー数・市場規模）は出典 URL を必ずつける
- 2年以上前の情報は「古い情報の可能性あり」と明記
- 確認できない情報は書かない

---

## STEP 2: 競合分析の整理

```markdown
## 競合分析

| ツール名 | 得意なこと | 欠けていること | 価格帯 |
|---------|-----------|--------------|-------|
| ...     | ...       | ...          | ...   |

## 直接競合なし / あり

[判断と理由]

## 差別化ポイント（このプロダクトが勝てる領域）
- ...

## 市場インサイト（ユーザーの不満・ニーズ）
- ...

## 出典
1. [タイトル](URL) — YYYY-MM-DD 参照
```

**`--yolo` モードでない場合**: ここで一時停止。「この分析で合っていますか？」と確認。

---

## STEP 3: STATE.md の更新

STATE.md の「競合分析」セクションに結果を追記。
「進捗チェックリスト」の「競合・市場リサーチ」を `[x]` にする。

---

## STEP 4: 次のアクション案内

**`--yolo` モードの場合**: 自動で `/project-prd` を実行。

**デフォルトの場合:**
```
競合リサーチ完了。STATE.md を更新しました。

次: /project-prd — 要件定義（PRD）+ 失敗シナリオ先読み
```
