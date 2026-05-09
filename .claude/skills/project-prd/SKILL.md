---
name: project-prd
description: /project-researchの後に実行。STATE.mdの情報をもとにPRD（要件定義）を生成しプレモーテム分析を行う。新プロジェクト開始フローのPhase 3。
when_to_use: 競合リサーチ完了後にPRDを作りたい、要件定義をしたい、プロジェクトの要件を整理したい
allowed-tools: Read Write Bash(git *) WebSearch
argument-hint: "[--yolo で確認なし実行]"
effort: high
context: fork
---

# /project-prd — PRD 生成 + プレモーテム（Phase 3）

## 現在の状態（自動取得）

!`cat docs/project/STATE.md 2>/dev/null || echo "STATE.md が見つかりません。"`

---

@../../templates/lib/yolo-mode.md

@steps/1-check.md

@steps/2-prd.md

@steps/3-premortem.md

@steps/4-save.md

@steps/5-next.md
