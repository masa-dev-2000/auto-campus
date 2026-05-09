---
name: state-check
description: docs/project/STATE.mdの存在確認。存在しない場合は/new-projectの実行を促して終了する。project-*スキルのステップから参照される。
when_to_use: プロジェクトフローの前提確認が必要なとき
allowed-tools: Bash(test *)
effort: low
---

## STATE.md チェックの振る舞い

`docs/project/STATE.md` が存在しない場合:
「先に `/new-project` を実行してください」と伝えて終了する。

STATE.md が存在する場合:
「一言サマリー」を読み込んで、何を作るプロジェクトかを把握してから次へ進む。
