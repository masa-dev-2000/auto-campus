---
name: init
description: 新リポジトリに .claude/ を移植した直後に実行。CLAUDE.md・docs/ 構造・.gitignore を生成し、次に打つコマンドまで案内する。
when_to_use: プロジェクトを始めたい、初期セットアップをしたい、.claude/ を移植した、新しいリポジトリで開発を始めたい
allowed-tools: Read Write Bash(git *) Bash(mkdir *) Bash(ls *) Bash(touch *) Bash(test *)
effort: low
context: fork
---

# /init — プロジェクト初期セットアップ

## 現在の状態を確認（自動取得）

!`echo "--- 現在の状態 ---" && echo "Git: $(git rev-parse --is-inside-work-tree 2>/dev/null && echo '初期化済み' || echo '未初期化')" && echo "CLAUDE.md: $(test -f CLAUDE.md && echo '存在する（上書き確認が必要）' || echo 'なし（新規作成します）')" && echo "docs/: $(test -d docs && echo '存在する' || echo 'なし（作成します）')" && echo "------------------"`

---

## ようこそ！

このコマンドは **3分** でプロジェクトの土台を作ります。

作成されるもの:
- `CLAUDE.md` — Claude が毎セッション読む「プロジェクトの記憶」
- `docs/` フォルダ — 要件定義・フェーズ計画・ADR を置く場所
- `.gitignore` — 基本的な除外設定

完了後に「次に打つコマンド」を案内します。

---

## STEP 1: 事前確認

### CLAUDE.md がすでに存在する場合

上書きしてよいか確認してください:
```
CLAUDE.md がすでに存在します。上書きしてよいですか？
（このプロジェクト専用の内容に書き直します）
```

「いいえ」と言われた場合はここで終了してください。

---

## STEP 2: ヒアリング（3問だけ）

以下を **1つのメッセージでまとめて** 質問してください:

```
3つだけ教えてください。答えが短くて全然OK です。

1. プロジェクト名は？（例: meeting-helper, todo-app）
2. 一言でいうと何を作りますか？（例: 会議の文字起こしアプリ）
3. 誰のためのツールですか？（例: 自分用、チーム向け）
```

回答を受け取ったら STEP 3 に進んでください。

---

## STEP 3: ファイル生成

### CLAUDE.md を生成

ヒアリング回答を埋めて `CLAUDE.md` を作成してください:

```markdown
# [プロジェクト名]

このファイルを読んだ証明として、最初の返答で必ず「[プロジェクト名] セッション開始」と述べること。

## プロジェクト概要

[一言説明]
ターゲット: [誰のためか]

## ドキュメント構造

\`\`\`
docs/
├── project/
│   ├── overview.md      # 要件定義（/project-prd で生成）
│   └── phases.md        # フェーズ計画（/project-plan で生成）
└── adr/                 # 技術選定の記録（/tech-select で生成）
\`\`\`

## 開発スタック

（/project-plan 完了後に追記）

## コマンド

（実装後に追記）

## 規約

- コメントは日本語で書く
- コミットは1ファイル1コミットを基本とする

## フェーズ状況

現在: 要件定義前
次のステップ: /new-project を実行
```

### docs/ フォルダ構造を作成

```bash
mkdir -p docs/project
mkdir -p docs/adr
touch docs/adr/.gitkeep
```

### .gitignore を作成（存在しない場合のみ）

存在する場合はスキップ。存在しない場合は以下を作成:

```
# 環境変数
.env
.env.local
.env.*.local

# Python
__pycache__/
*.py[cod]
*.egg-info/
.venv/
venv/
dist/
.pytest_cache/

# Node.js
node_modules/
.next/
dist/
.cache/

# Rust / Tauri
target/

# OS
.DS_Store
Thumbs.db

# IDE
.vscode/settings.json
.idea/
```

---

## STEP 4: git コミット

git が未初期化の場合は `git init` を実行してから進む。

以下の順番で **1ファイル1コミット** でコミット:

1. `.gitignore` があれば先にコミット
2. `docs/adr/.gitkeep` をコミット
3. `CLAUDE.md` をコミット

---

## STEP 5: 完了報告（ここが一番大事）

以下の形式で **丁寧に** 案内してください:

```
セットアップ完了！ 🎉

作成したファイル:
  CLAUDE.md          — プロジェクトの記憶（Claude が毎回読みます）
  docs/project/      — 要件定義・フェーズ計画の置き場所
  docs/adr/          — 技術選定の記録の置き場所
  .gitignore         — 除外設定

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
次のステップ（この順番でやります）
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

STEP 1: 要件定義
  /new-project
  → 5つの質問に答えるだけで要件定義が完成します

STEP 2: 競合リサーチ
  /project-research
  → Webで競合を自動調査します（/new-project が自動で呼びます）

STEP 3: PRD（製品要件書）の作成
  /project-prd
  → 作るべき機能を P0/P1/P2 で整理します

STEP 4: 技術選定 + フェーズ計画
  /project-plan
  → 技術スタックを決めて、Phase 0 から始める実装計画を作ります

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
全部まとめて一気にやりたい場合:
  /new-project --yolo
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

まずは /new-project から始めてみてください！
```

---

## 注意

- STEP 2 のヒアリングは省略しないこと。たった3問なので必ず聞く
- `.gitignore` はすでに存在する場合は上書きしない
- 完了報告のコマンド案内は省略しない。これが一番重要
