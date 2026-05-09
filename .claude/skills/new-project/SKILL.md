---
name: new-project
description: 新プロジェクト開始・アイデアをPRDに落とす・フェーズ計画を作る。「何を作りたいか」さえあれば競合リサーチ・要件定義・技術選定・phases.md生成まで一気に完了する。
when_to_use: 新しいプロダクトやツールを作りたい、要件定義書を作りたい、プロジェクトの計画を立てたい、アイデアを整理したい
allowed-tools: Read Write Bash(mkdir *) Bash(git *) Bash(ls *) WebSearch WebFetch
argument-hint: "[作りたいもの（省略可）] [--yolo で全自動]"
effort: high
context: fork
---

# /new-project — アイデア → プロジェクト設計

## 現在の状態（自動取得）

!`ls docs/project/ 2>/dev/null && echo "---" && cat docs/project/STATE.md 2>/dev/null || echo "STATE.md なし（新規）"`

---

## 実行モード判定

`$ARGUMENTS` を確認してください:

- **`--yolo` が含まれる場合**: STEP 1〜3 を連続実行し、完了後に `/project-research` `/project-prd` `/project-plan` を自動で順番に呼び出す。確認ゲートはスキップ。
- **デフォルト（`--interactive`）**: STEP 1 のヒアリングのみ実行して一時停止。続きは後続コマンドを個別に呼ぶ。

---

## STEP 1: アイデアのヒアリング

`docs/project/STATE.md` が存在する場合はそれを読み込み、未回答の質問があれば続きから始めてください。

以下の5問を**一度にまとめて**提示してください。

```
以下の5問に答えてください:

1. 何を作りたいですか？（1〜2文で）
2. 誰のためですか？（具体的な1人を想像して）
3. そのユーザーは今、同じ問題をどう解決していますか？（現在の代替手段）
4. なぜ今の解決策では不十分なのですか？
5. このプロダクトが「成功した」と言えるのはどんな状態ですか？
```

回答後:
1. 1文で確認 → 「つまり、〇〇が〇〇するために〇〇を作る、ということですね。」
2. `docs/project/STATE.md` を以下のテンプレートで作成（テンプレート: @templates/state-template.md）

---

## STEP 2: STATE.md の保存

STATE.md に以下を記録:

```markdown
# プロジェクト STATE

最終更新: YYYY-MM-DD

## ヒアリング回答
- 作るもの: ...
- ターゲットユーザー: ...
- 現在の代替手段: ...
- 代替手段の不足: ...
- 成功の定義: ...

## 一言サマリー
つまり、〇〇が〇〇するために〇〇を作る。

## 進捗
- [x] STEP 1: ヒアリング完了
- [ ] 競合リサーチ（/project-research）
- [ ] PRD生成（/project-prd）
- [ ] フェーズ計画（/project-plan）
```

---

## STEP 3: 次のアクション案内

**`--yolo` モードの場合**: 自動で `/project-research` を実行してください。

**デフォルトモードの場合**: 以下を案内してください。

```
ヒアリング完了。STATE.md を保存しました。

次のステップ:
  /project-research  — 競合・市場リサーチ（所要時間: 5〜10分）
  /project-prd       — 要件定義（PRD）生成
  /project-plan      — 技術選定 + フェーズ計画

すべてをまとめて実行したい場合: /new-project --yolo
```

---

## 注意

- STEP 1 のヒアリングを飛ばさないこと。`$ARGUMENTS` に何か書いてあっても5問は必ず確認する
- 「たぶんこうだろう」で進めないこと。不明なら必ず聞く
- `--yolo` モードでも STATE.md への保存は省略しない
