---
name: project-research
description: /new-projectの後に実行。STATE.mdの情報をもとに競合分析・市場調査を行いSTATE.mdを更新する。新プロジェクトの競合調査フェーズ専用。
when_to_use: /new-projectのヒアリング完了後に競合を調べたい、プロジェクトの市場調査をしたい
allowed-tools: Read Write Bash(mkdir *) Bash(git *) WebSearch WebFetch
argument-hint: "[--yolo で確認なし実行]"
effort: medium
context: fork
---

# /project-research — 競合・市場リサーチ（Phase 2）

## 現在の状態（自動取得）

!`cat docs/project/STATE.md 2>/dev/null || echo "STATE.md が見つかりません。"`

---

@../../templates/lib/yolo-mode.md

@steps/1-read.md

@steps/2-research.md

@steps/3-update.md

@steps/4-next.md
