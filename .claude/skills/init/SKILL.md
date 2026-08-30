---
name: init
description: 新リポジトリに .claude/ を移植した直後に実行。CLAUDE.md・docs/ 構造・.gitignore を生成し、次に打つコマンドまで案内する。
when_to_use: プロジェクトを始めたい、初期セットアップをしたい、.claude/ を移植した、新しいリポジトリで開発を始めたい
allowed-tools: Read Write Bash(mkdir *) Bash(ls *) Bash(touch *) Bash(cp *) Bash(test *)
effort: low
context: fork
---

# /init — プロジェクト初期セットアップ

## 現在の状態（自動取得）

!`echo "Git: $(git rev-parse --is-inside-work-tree 2>/dev/null && echo '初期化済み' || echo '未初期化')" && echo "CLAUDE.md: $(test -f CLAUDE.md && echo '存在する（上書き確認あり）' || echo 'なし → 新規作成')" && echo "docs/: $(test -d docs && echo '存在する' || echo 'なし → 新規作成')"`

---

ようこそ！このコマンドは **3分** でプロジェクトの土台を作ります。

@steps/1-check.md

@steps/2-ask.md

@steps/3-create.md

@steps/4-commit.md

@steps/5-guide.md
