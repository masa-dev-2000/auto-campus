---
name: research
description: 競合分析・市場調査・技術比較・ユーザーニーズ調査。「〇〇を調べて」「競合を分析して」「技術Aと技術Bを比較して」という文脈で起動する。判断材料となる情報を出典付きレポートにまとめる。
when_to_use: 競合を調べたい、市場を調査したい、技術を比較したい、ユーザーの不満やニーズを調べたい、判断材料が欲しい
allowed-tools: Read Write Bash(mkdir *) Bash(git *) WebSearch WebFetch
argument-hint: "[リサーチテーマ（省略可）] [--yolo で確認なし実行]"
effort: medium
context: fork
---

# /research — 競合・市場・技術リサーチ

## 既存リサーチ（自動取得）

!`ls docs/research/ 2>/dev/null | head -10 || echo "docs/research/ なし（初回）"`

---

@../../templates/lib/yolo-mode.md

@steps/1-theme.md

@steps/2-search.md

@steps/3-report.md

@steps/4-save.md
