---
name: new-project
description: 新プロジェクト開始・アイデアをPRDに落とす・フェーズ計画を作る。「何を作りたいか」さえあれば競合リサーチ・要件定義・技術選定・phases.md生成まで一気に完了する。
when_to_use: 新しいプロダクトやツールを作りたい、要件定義書を作りたい、プロジェクトの計画を立てたい、アイデアを整理したい
allowed-tools: Read Write Bash(mkdir *) Bash(git *) Bash(ls *)
argument-hint: "[作りたいもの（省略可）] [--yolo で全自動]"
effort: high
context: fork
---

# /new-project — アイデア → プロジェクト設計（Phase 1）

## 現在の状態（自動取得）

!`ls docs/project/ 2>/dev/null && echo "---" && cat docs/project/STATE.md 2>/dev/null || echo "STATE.md なし（新規）"`

---

@../../templates/lib/yolo-mode.md

@steps/1-hear.md

@steps/2-save.md

@steps/3-next.md
