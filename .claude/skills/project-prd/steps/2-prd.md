## STEP 2: PRD 生成

`docs/project/hearing.md` と競合分析（STATE.md）をもとに PRD を生成する。

テンプレート参照: @../../templates/prd.md

**重要ルール:**
- P0 は最大3機能。超える場合はユーザーに絞り込みを促す
- P0 の受け入れ基準は **EARS形式** で書く: `WHEN [状況] THEN [システムは○○すること]`
- **Rabbit Holes** と **No-gos** を必ず分けて書く
  - Rabbit Holes: やり始めると無限に広がる領域（スコープの限界を明示）
  - No-gos: 意図的に作らないもの（理由を必ず書く）
- 未決定事項は「問い・オーナー・期限・影響」の4列テーブルで書く

@../../confirm-gate/SKILL.md
