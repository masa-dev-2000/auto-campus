---
name: project-plan
description: /project-prdの後に実行。技術選定・フェーズ計画を生成しCLAUDE.mdとphases.mdを完成させる。新プロジェクト開始フローのPhase 4（最終）。
when_to_use: PRD完了後にフェーズ計画を作りたい、技術スタックを決めたい、phases.mdを生成したい
allowed-tools: Read Write Bash(git *) Bash(ls *) WebSearch WebFetch
argument-hint: "[--yolo で確認なし実行]"
effort: high
context: fork
---

# /project-plan — 技術選定 + フェーズ計画（Phase 4 / 最終）

## 現在の状態（自動取得）

!`cat docs/project/STATE.md 2>/dev/null | head -40 || echo "STATE.md なし。先に /project-prd を実行してください。"`

---

@../../templates/lib/yolo-mode.md

@steps/1-check.md

@steps/2-tech.md

@steps/3-phases.md

@steps/4-save.md

@steps/5-cleanup.md
