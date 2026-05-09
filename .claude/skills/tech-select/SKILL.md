---
name: tech-select
description: 技術選定・技術比較・アーキテクチャ決定をADRとして記録する。「データベースをどれにすべきか」「STTプロバイダーを比較したい」「技術選定の記録を残したい」という文脈で起動する。
when_to_use: 技術を選びたい、ライブラリを比較したい、アーキテクチャを決めたい、技術選定の記録を残したい、ADRを作りたい
allowed-tools: Read Write Bash(ls *) Bash(mkdir *) Bash(git *) WebSearch WebFetch
argument-hint: "[技術カテゴリ（例: データベース、STTプロバイダー）] [--yolo で確認なし実行]"
effort: medium
context: fork
---

# /tech-select — 技術選定 + ADR 自動生成

## 既存 ADR（自動取得）

!`ls docs/adr/*.md 2>/dev/null | grep -v gitkeep | sort || echo "ADR なし（初回）"`

---

@../../templates/lib/yolo-mode.md

@steps/1-input.md

@steps/2-research.md

@steps/3-compare.md

@steps/4-score.md

@steps/5-adr.md
