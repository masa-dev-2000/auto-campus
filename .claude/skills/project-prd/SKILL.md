---
name: project-prd
description: /project-researchの後に実行。STATE.mdの情報をもとにPRD（要件定義）を生成しプレモーテム分析を行う。新プロジェクト開始フローのPhase 3。
when_to_use: 競合リサーチ完了後にPRDを作りたい、要件定義をしたい、プロジェクトの要件を整理したい
allowed-tools: Read Write Bash(git *) WebSearch
argument-hint: "[--yolo で確認なし実行]"
effort: high
context: fork
---

# /project-prd — PRD 生成 + プレモーテム（プロジェクト開始フロー Phase 3）

## 前提コンテキスト（自動取得）

!`cat docs/project/STATE.md 2>/dev/null || echo "STATE.md が見つかりません。先に /new-project → /project-research を実行してください。"`

---

## 事前確認

STATE.md が存在しない、または競合分析が未完了の場合: **「先に `/project-research` を実行してください」と伝えて終了。**

---

## STEP 1: PRD 生成

STATE.md のヒアリング回答・競合分析をもとに PRD を生成。
テンプレート参照: @../../docs/templates/prd.md

```markdown
## プロダクト概要（1文）
[誰が / 何のために / 何をする]

## ターゲットユーザー
[具体的な1人の人物像。名前・職業・状況・不満を書く]

## コア機能

### P0（最大3つ。これがないとプロダクトではない）
| # | 機能名 | 概要 | 完了条件 |
|---|--------|------|----------|

### P1（あると価値が大幅に上がる）
| # | 機能名 | 概要 |
|---|--------|------|

### P2（将来対応）
| # | 機能名 | 概要 |
|---|--------|------|

## スコープ外（意図的に作らないもの）
- ...（理由: ...）

## 成功指標（数値で測れるもの）
| 指標 | 目標値 | 計測方法 |
```

**重要ルール:**
- P0 は最大3機能。超える場合はユーザーに絞り込みを促す
- スコープ外を必ず書く。「やらないこと」の明示が後の混乱を防ぐ

**`--yolo` でない場合**: ここで一時停止。「P0 の機能に抜け漏れはありますか？」と確認。

オプション: **Don Norman** エージェントに「P0 機能の UX リスク」をレビューさせますか？

---

## STEP 2: プレモーテム（失敗シナリオ先読み）

*glebis/decision-toolkit より採用*

Claude 自身が考えて出力:

```
このプロジェクトが6ヶ月後に失敗しているとしたら、
最もありそうな理由を5つ挙げてください。
それぞれに対して設計段階でできる対策を添えてください。
```

「この中で特に対策を強化したいリスクはありますか？」と確認。
回答を STATE.md の「PRD」セクションに追記。

**`--yolo` でない場合**: ここで一時停止。

---

## STEP 3: docs/project/overview.md の保存

`docs/project/overview.md` に PRD 全文を保存してコミット:
```
git add docs/project/overview.md
git commit -m "docs: add PRD for [プロジェクト名]"
```

STATE.md の「進捗チェックリスト」の「PRD + プレモーテム」を `[x]` にする。

---

## STEP 4: 次のアクション案内

**`--yolo` の場合**: 自動で `/project-plan` を実行。

**デフォルトの場合:**
```
PRD 完了。docs/project/overview.md を保存しました。

次: /project-plan — 技術選定 + フェーズ計画生成
```
