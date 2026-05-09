# Claude Code ノウハウ集

先人の実例と公式ドキュメントから収集。随時更新する。

---

## 1. CLAUDE.md（最重要）

Claude は毎セッション開始時にプロジェクトルートの `CLAUDE.md` を自動で読む。これがプロジェクトの「記憶」。

### 書くべき内容
- プロジェクト概要（1〜3行）
- 使用スタックとバージョン
- 開発・テスト・デプロイのコマンド
- ディレクトリ構造の説明
- コーディング規約・禁止事項
- 外部サービスと環境変数

### 運用ルール
- **200行以内**に収める（長すぎるとコンテキストを圧迫して逆効果）
- **チームで育てる**: Claude が間違えたらすぐ追記する
- Claude がデフォルトで正しくやることは書かない（過剰仕様にしない）
- カナリアパターン: 「このファイルを読んだら〇〇と言え」で読み込み確認

出典: Boris Cherny (Claude Code 作者) https://github.com/0xquinto/bcherny-claude

---

## 2. Plan Mode（実装前の設計分離）

実装を依頼する前に、まず設計だけを出力させるのが鉄則。

### ワークフロー
1. 「プランだけ書いて、実装はまだしないで」と指示
2. エディタでプランを開き、間違い・懸念点にコメントを追加
3. 「コメントに全部対応して、まだ実装しないで」と返す
4. 合意できたら「実装して」

### なぜ重要か
一発実装を頼むと方向性のズレで後から大量修正が発生する。
「複雑なタスクはプランから始め、プランに全力を注げば実装は一発で通る」 — Boris Cherny

出典: https://levelup.gitconnected.com/claude-code-best-practices-12-patterns-agentic-engineers-use-65264e3eb919

---

## 3. Context Rot（コンテキスト劣化）対策

長いセッションで Claude の回答品質が落ちてきたら、コンテキストウィンドウが詰まっているサイン。

### 対策
- 同じ間違いを2回繰り返したら → `/clear` してプロンプトを書き直す
- 無関係なタスクは別セッション（1セッション1テーマ）
- 大きなファイルの探索はサブエージェントに委譲（要約だけが本体に戻る）
- 200行を超えるファイルはコンテキストを大量消費する

出典: https://angelo-lima.fr/en/claude-code-context-memory-management/

---

## 4. Git Worktree + 並行セッション

Boris Cherny は常時 10〜15 セッションを並行稼働させている。

```bash
git worktree add ../feature-auth feature/auth
git worktree add ../feature-db feature/db
```

各 worktree で独立した Claude セッションを走らせることで変更が衝突しない。
チームでも有効な手法。

出典: https://mindwiredai.com/2026/03/25/claude-code-creator-workflow-claudemd/

---

## 5. Hooks（決定論的な自動化）

Claude の行動に頼らず、確実に実行したい処理をシェルコマンドで挟む仕組み。

### 設定場所
- `.claude/settings.json` → プロジェクト固有
- `~/.claude/settings.json` → 全プロジェクト共通

### よくある使い方
```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{"type": "command", "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\" 2>/dev/null || true"}]
    }],
    "PreToolUse": [{
      "matcher": "Edit|Write",
      "hooks": [{"type": "command", "command": "if [[ $(git branch --show-current) == 'main' ]]; then echo 'main ブランチへの直接編集は禁止'; exit 2; fi"}]
    }]
  }
}
```

### 実例（先人）
| フック | 内容 | 出典 |
|---|---|---|
| PostToolUse 自動フォーマット | Prettier/black を毎回実行 | Boris Cherny, ChrisWiles |
| PreToolUse main 保護 | main への直接編集をブロック | ChrisWiles/claude-code-showcase |
| PostToolUse 絵文字除去 | 全ファイルの絵文字を自動削除 | ZacheryGlass |
| Stop フック | 「残作業があれば続けろ」リマインド | Boris Cherny |
| SessionStart | ブランチ名・最終コミットを表示 | Boris Cherny |

出典:
- https://github.com/ChrisWiles/claude-code-showcase
- https://github.com/ZacheryGlass/.claude
- https://github.com/disler/claude-code-hooks-mastery

---

## 6. カスタムスラッシュコマンド

`.claude/commands/` にマークダウンファイルを置くと `/コマンド名` で呼べる。

```
.claude/commands/
  review.md    → /review
  deploy.md    → /deploy
```

### Boris Cherny のコマンド集
| コマンド | 内容 |
|---|---|
| `/commit-push-pr` | diff確認 → conventional commit → push → PR作成を一発 |
| `/grill` | adversarial コードレビュー（APPROVE/REJECT判定付き） |
| `/test-and-fix` | テストが全部通るまでループし続ける |
| `/worktree` | 並行セッション用 git worktree を作成 |
| `/techdebt` | セッション終了後の技術負債クリーンアップ |

「毎回同じプロンプトを書いている」と気づいたらコマンド化するタイミング。

出典: https://github.com/0xquinto/bcherny-claude

---

## 7. サブエージェント

`.claude/agents/` に定義を置くと、Claude が独立したコンテキストで別 Claude を呼べる。

### 活用パターン
- 探索専用（読み取り専用・高速）
- セキュリティレビュー専用
- テスト生成専用

### コスト最適化
「メインは Opus、サブは Haiku」という使い分けで品質を保ちながらコストを削減。

出典: https://www.pubnub.com/blog/best-practices-for-claude-code-sub-agents/

---

## 8. MCP サーバー

Claude が GitHub・Notion・Supabase 等を直接操作できるようになる拡張。

### 注意点
インストールしすぎると各ツール定義がコンテキストを消費する。
（20サーバー × 5ツール = 数千トークン）本当に必要なものだけ入れる。

`ENABLE_TOOL_SEARCH: "true"` を設定すると、必要なときだけツール定義をロードできる。

出典: https://www.builder.io/blog/claude-code-mcp-servers

---

## 9. settings.json 優先順位

```
Managed → CLI args → settings.local.json → settings.json → ~/.claude/settings.json
```

プロジェクト固有の設定は `.claude/settings.json`（git 管理）。
個人の上書きは `.claude/settings.local.json`（gitignore 推奨）。

---

## 10. Andrej Karpathy 由来の4原則

シンプルかつ本質的。CLAUDE.md に入れるべき思想。

1. **コードより先に考える**: 推測するな。混乱を隠すな。トレードオフを明示しろ
2. **シンプル優先**: 問題を解く最小限のコードだけ書け。投機的な実装は禁止
3. **外科的な変更**: 隣接コードを勝手に「改善」するな
4. **ゴール起点で動く**: 成功条件を定義しろ。確認するまでループしろ

出典: https://github.com/forrestchang/andrej-karpathy-skills/blob/main/CLAUDE.md

---

## 参考リポジトリ

| リポジトリ | 内容 |
|---|---|
| https://github.com/shanraisshan/claude-code-best-practice | 69のベストプラクティス集 |
| https://github.com/ChrisWiles/claude-code-showcase | 本格的な .claude/ 構成例 |
| https://github.com/iannuttall/claude-sessions | セッション記録コマンド集 |
| https://github.com/disler/claude-code-hooks-mastery | 全ライフサイクルフックの実装例 |
| https://github.com/affaan-m/everything-claude-code | 48エージェント・182スキルの大規模構成 |
| https://github.com/wshobson/commands | 57の本番用カスタムコマンド集 |
