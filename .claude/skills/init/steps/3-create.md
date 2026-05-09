## STEP 3: ファイル生成

### CLAUDE.md

プロジェクト名を埋めて作成する:

```markdown
# [プロジェクト名]

このファイルを読んだ証明として、最初の返答で必ず「[プロジェクト名] セッション開始」と述べること。

## プロジェクト概要

（/new-project のヒアリングシート完了後に追記）

## ドキュメント構造

\`\`\`
docs/
├── cc-knowhow.md        # Claude Code のノウハウ集
├── dev-methodology.md   # 開発プロジェクトの進め方
└── project/
    ├── hearing.md       # ヒアリングシート（/new-project で生成）
    ├── overview.md      # 要件定義（/project-prd で生成）
    └── phases.md        # フェーズ計画（/project-plan で生成）
\`\`\`

## 開発スタック

（/project-plan 完了後に追記）

## コマンド

（実装後に追記）

## 規約

- コメントは日本語で書く
- コミットは1ファイル1コミットを基本とする

## フェーズ状況

現在: 環境セットアップ完了
次のステップ: /new-project を実行
```

### docs/ フォルダ構造

```bash
mkdir -p docs/project
mkdir -p docs/adr
touch docs/adr/.gitkeep
```

### ノウハウドキュメントをコピー

```bash
cp .claude/templates/cc-knowhow.md docs/cc-knowhow.md
cp .claude/templates/dev-methodology.md docs/dev-methodology.md
```

### .gitignore（存在しない場合のみ作成）

```
.env
.env.local
__pycache__/
*.py[cod]
.venv/
venv/
node_modules/
target/
.DS_Store
Thumbs.db
.vscode/settings.json
```
