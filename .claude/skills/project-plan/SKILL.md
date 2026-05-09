---
name: project-plan
description: /project-prdの後に実行。技術選定・フェーズ計画を生成しCLAUDE.mdとphases.mdを完成させる。新プロジェクト開始フローのPhase 4（最終）。
when_to_use: PRD完了後にフェーズ計画を作りたい、技術スタックを決めたい、phases.mdを生成したい
allowed-tools: Read Write Bash(git *) Bash(ls *) WebSearch WebFetch
argument-hint: "[--yolo で確認なし実行]"
effort: high
context: fork
---

# /project-plan — 技術選定 + フェーズ計画（プロジェクト開始フロー Phase 4 / 最終）

## 前提コンテキスト（自動取得）

!`cat docs/project/STATE.md 2>/dev/null || echo "STATE.md なし"` 
!`cat docs/project/overview.md 2>/dev/null | head -40 || echo "overview.md なし。先に /project-prd を実行してください。"`

---

## 事前確認

overview.md が存在しない場合: **「先に `/project-prd` を実行してください」と伝えて終了。**

---

## STEP 1: 技術選定（サマリーレベル）

P0 機能を実現するための技術カテゴリを列挙し、各カテゴリで推奨技術を選定。
`WebSearch` で最新の価格・ベンチマークを確認。

詳細な比較検討が必要なカテゴリは `/tech-select` の実行を提案してください。

```markdown
## 技術選定

| 役割 | 採用 | 理由（1文） | 却下した選択肢 |
|-----|------|------------|--------------|
| バックエンド | ... | ... | ... |
| フロントエンド | ... | ... | ... |
| データベース | ... | ... | ... |
| ...  | ... | ... | ... |
```

オプション: **Uncle Bob** エージェントに「技術選定がアーキテクチャの依存方向を壊さないか」を確認させますか？

**`--yolo` でない場合**: ここで一時停止。「この技術スタックでよいですか？」と確認。

---

## STEP 2: フェーズ計画生成

`docs/project/phases.md` を生成。Phase 0 は「核心技術が動くことの最小確認」に絞ること。

```markdown
# フェーズ・タスク一覧

## 現在のフェーズ
**Phase 0（PoC）** — 未着手

---

## Phase 0 — [核心技術の動作確認]

**完了条件**: [数値で測れる1文。例: 「〇〇が△△秒以内に画面に表示される」]

### Step 0-1: 環境構築
- [ ] 必要ツールのインストール（バージョン明記）
- [ ] `.env.example` 作成（必要なAPIキー一覧）
- [ ] `.gitignore` 作成

### Step 0-2: [最小機能の実装]
- [ ] ...
- [ ] **完了条件**: ...

---

## Phase 1 — [コア機能実装]

**前提**: Phase 0 完了
**完了条件**: [P0 機能がすべて動く状態]

（Phase 2, 3 と続く）
```

**`--yolo` でない場合**: ここで一時停止。「このフェーズ計画でよいですか？」と確認。

---

## STEP 3: 成果物の保存（1ファイル1コミット）

以下の順番で保存:

1. `docs/project/phases.md` をコミット
2. `CLAUDE.md` を更新（プロジェクト概要・スタック・フェーズ状況）してコミット
3. `docs/project/STATE.md` を削除してコミット（フロー完了）

---

## STEP 4: 完了報告

```
プロジェクト設計が完了しました 🎉

生成したファイル:
  docs/project/overview.md  — PRD（要件定義）
  docs/project/phases.md    — フェーズ計画
  CLAUDE.md                 — 更新済み

次のステップ:
  1. Deepgram などの必要な API キーを取得して .env に記載
  2. phases.md の Phase 0 Step 0-1 から開始

技術選定の詳細 ADR を記録したい場合: /tech-select [カテゴリ名]
```

---

## 注意

- Phase 0 は「核心技術が動くことの最小確認」だけに絞る。フロントエンドや API は後回し
- 技術選定は `/tech-select` で詳細な ADR を別途作成することを推奨する
