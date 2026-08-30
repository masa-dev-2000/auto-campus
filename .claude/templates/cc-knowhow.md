# Claude Code ノウハウ集

先人の実例と公式ドキュメントから収集。随時更新する。

凡例:
- **[公開]** — 本人が公言・記事・公開リポジトリで確認済み
- **[非公開]** — 存在は示唆されているが内容が公開されていない
- **[推測]** — 状況証拠からの論理的推定。本人未確認

---

## 計画立案・開発手法（2026年最新）

### Anthropic 公式：Explore → Plan → Implement → Commit **[公開]**

2026年に「名前のついた標準」として確立した4フェーズ。

```
1. Explore   — Plan Mode でファイルを読む（コードは書かない）
2. Plan      — プランを文書として出力させる
3. Implement — 承認後に一括実装（途中で止めない）
4. Commit    — 説明的なコミット + PR 作成
```

**なぜ重要か**: フェーズ間でコンテキストをクリアすることで、調査フェーズの情報が実装フェーズを汚染しない。

出典: https://code.claude.com/docs/en/best-practices / https://code.claude.com/docs/en/common-workflows

---

### Boris Cherny の実際のワークフロー **[公開]**

InfoQ・Pragmatic Engineer 等の複数メディアが独立して検証済み。

**計画フェーズの詳細**
- 全セッションの約 80% を Plan Mode（`--permission-mode plan`）で開始
- Plan Mode は read-only state。Claude はファイルを読めるが一切書けない
- Claude がプランを出力したらエディタで開き、**インライン注釈**を直接書き込む
  - 「この前提は間違い」「このアプローチは禁止」「ドメイン知識の補足」など
- 「注釈に対応して、まだ実装しないで」と返す
- このループを **1〜6回** 繰り返してプランを確定する
- 確定後、auto-accept edits モードで中断なしに一括実装させる

**規模感**
- 常時 10〜15 並行セッション（ターミナル 5 + ブラウザ 5〜10）
- 1日 10〜30 PR をマージ
- コンテキスト使用率 **60% に達したら必ずクリア**（コンテキストが主要なスカース資源）

出典: https://www.infoq.com/news/2026/01/claude-code-creator-workflow/ / https://howborisusesclaudecode.com/

---

### AskUserQuestion 技法（要件引き出し） **[公開]**

大きな機能の前に「Claude にインタビューさせる」手法。Anthropic 公式推奨。

```
1. 最小限のプロンプトを渡す（ゴール1行だけ）
2. Claude が AskUserQuestion で不明点を次々と引き出す
   （技術実装・UX・エッジケース・トレードオフ）
3. すべて回答したら Claude が SPEC.md に書き出す
4. 新鮮なセッションを開いて SPEC.md を元に実装する
```

要件定義と実装を完全に分離することで、実装中に「そもそも何を作りたいか」が揺れない。

出典: https://code.claude.com/docs/en/best-practices

---

### Ultrathink **[公開]**

プロンプトに `ultrathink` と書くだけで最大の推論バジェットを使わせる。

```
「この機能の実装戦略を計画して。まだコードは書かないで。ultrathink」
```

- **CLI 専用**（Web UI・API では効かない）
- Plan Mode 中に使うのがベストプラクティス（計画に深い思考、実装は高速モデルで）
- 類似: `/effort max`、「think harder」、「think step by step」

出典: https://claudelog.com/faqs/what-is-ultrathink/ / https://kentgigger.com/posts/claude-code-thinking-triggers

---

### Ultraplan（2026年4月リリース） **[公開]**

`/ultraplan` で計画フェーズをクラウドにオフロードする機能。

```
ローカル CLI → /ultraplan → Anthropic 管理のクラウドコンテナで計画を実行
                         → ブラウザでプランをレビュー・インラインコメント
                         → CLI に戻して実装 or クラウドで実行
```

- プランが**永続的なクラウド成果物**になる（CLI 再起動しても消えない）
- チームメンバーとプランを共有可能
- Pro/Max サブスクリプション + Claude Code v2.1.91+ 必要
- 1回のフルセッションで Pro の 5時間制限の約 33% を消費

**推奨運用**: ローカル Plan Mode で方向性を確認 → 複雑なタスクのみ Ultraplan で精緻化

**[推測]** Ultraplan の内部に「Mythos」と呼ばれるマルチエージェント計画基盤があるという情報が Medium 記事で言及されているが、Anthropic は確認していない。

出典: https://devops.com/claude-codes-ultraplan-bridges-the-gap-between-planning-and-execution/ / https://code.claude.com/docs/en/whats-new/2026-w15

---

### Spec-Driven Development（仕様駆動開発） **[公開]**

O'Reilly ライブイベントが開催されるほど普及した手法。

```
1. 仕様書（SPEC.md）を自然言語で書く
   （何を作るか・制約・完了条件・作らないこと）
2. Claude に spec をレビューさせ、曖昧点を洗い出す
3. spec を確定してから初めて実装を依頼
4. 実装後に spec との差分をレビュー
```

TDD の「テストを先に書く」に対応する「仕様を先に書く」アプローチ。
コードはあくまで spec から生成される出力物という思想。

OSS ツール: https://github.com/gotalab/cc-sdd

出典: https://agentfactory.panaversity.org/docs/General-Agents-Foundations/spec-driven-development / https://alexop.dev/posts/spec-driven-development-claude-code-in-action/

---

### Planning-with-Files（永続メモリ設計） **[公開]**

Manus AI の手法に触発された。Meta が Manus を 20億ドルで買収（2025年12月）した背景もあり注目されている。

**3ファイルをディスク上の「作業メモリ」として使う**

```
task_plan.md   — フェーズ・チェックボックス・マイルストーン
findings.md    — リサーチ・発見・インサイト
progress.md    — セッションログ・テスト結果・エラー
```

**設計思想**: コンテキストウィンドウ = 揮発メモリ。ファイルシステム = 永続メモリ。

仕掛け:
- PreToolUse フックで主要な決定前に `task_plan.md` を再読（注意のアンカリング）
- 失敗はすべて progress.md に記録して同じ間違いを繰り返させない
- Stop フックで全フェーズ完了を検証してからセッション終了

**検証結果**: 96.7% のパス率（スキルなしは 6.7%）

出典: https://github.com/OthmanAdi/planning-with-files / https://www.blog.brightcoding.dev/2026/05/01/planning-with-files-the-2b-workflow-secret-every-developer-needs/

---

### LISA（仕様インタビューツール、2026年） **[公開]**

「Lisa が計画する。Ralph が実装する。」

Claude に AskUserQuestion でインタビューさせ、以下の3ファイルを自動生成するスキル:
- `PRD.md` — 要件定義書（マークダウン）
- `spec.json` — 構造化された仕様
- `progress.md` — 進捗トラッカー

`/lisa:plan` で起動。Spec-Driven Development の要件引き出しフェーズを自動化。

出典: https://github.com/blencorp/lisa

---

### Karpathy の最新思想（2026年1月） **[公開]**

X スレッドが出典。`forrestchang/andrej-karpathy-skills` が GitHub 10万スター超で拡散。

**ワークフローの変化**
- 2025年11月: 手動80% + AI 20%
- 2026年1月: AI 80% + 人間レビュー 20%
- 転換点: 2025年12月

**新しい主要スキルは「コードを書くこと」ではなく「意図の仕様化とタスク分解」**

> "Don't tell it what to do. Give it success criteria and watch it go."
>（何をすべきかを指示するな。成功基準を渡して見ていろ。）

**4原則（CLAUDE.md に入れるべき思想）**

| 原則 | 内容 |
|---|---|
| Think Before Coding | 推測するな。混乱を隠すな。トレードオフを明示しろ |
| Simplicity First | 問題を解く最小限のコード。投機的実装禁止 |
| Surgical Changes | 関係ないコードを「改善」するな。自分のメスだけきれいにしろ |
| Goal-Driven Execution | 成功基準を定義しろ。弱い基準（「動かせ」）は常に修正が必要になる |

出典: https://x.com/karpathy/status/2015883857489522876 / https://github.com/forrestchang/andrej-karpathy-skills

---

### Context Engineering（2026年の支配的パラダイム） **[公開]**

「プロンプトエンジニアリング」から「コンテキストエンジニアリング」へ。

**定義**: モデルが行動するときに持つ情報を意図的に設計すること。

```
コンテキスト = CLAUDE.md
            + サブエージェントの要約出力
            + 永続マークダウンファイル（findings.md 等）
            + フィルタされたツール結果
            + 構造化メモリ
```

調査対象チームの 89% がコンテキスト管理インフラへの投資を計画。

出典: https://blog.american-technology.net/context-engineering/

---

### Agent Teams（実験的、2026年Q1） **[公開]**

1つの Claude セッションがチームリーダーとして複数のサブセッションを調整する機能。

```bash
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=true claude
```

**ベストプラクティス**: 必ず Plan Mode を先に走らせてから Agent Teams を起動する。
「Plan Mode は約1万トークン。間違った方向で走るチームは 50万トークン+。」

出典: https://code.claude.com/docs/en/agent-teams

---

### Routines（クラウドスケジュール実行） **[公開]**

スケジュールまたはイベントトリガーで Claude Code セッションをクラウドで自動実行。
マシンがオフでも動く。

活用例:
- 毎晩のイシュートリアージ
- 週次のドキュメント乖離検出
- PR トリガーのコードレビュー

出典: https://nimbalyst.com/blog/claude-code-routines-practical-guide/

---

## 公開 / 非公開 / 推測 の全体マップ

### 確実に公開されているもの

| 手法 | 出典 |
|---|---|
| Explore→Plan→Implement→Commit | Anthropic 公式ドキュメント |
| Boris Cherny のインラインアノテーション計画 | InfoQ / Pragmatic Engineer |
| Ultrathink（計画中に最大推論を使う） | 公式ドキュメント |
| Ultraplan（クラウド計画オフロード） | 公式リリースノート（2026年4月） |
| Spec-Driven Development | OSS + O'Reilly イベント |
| Planning-with-Files | OSS (OthmanAdi/planning-with-files) |
| LISA（インタビュー → 仕様生成） | OSS (blencorp/lisa) |
| Karpathy の4原則 + 意図仕様化 | X スレッド + 10万スター GitHub |
| Context Engineering パラダイム | 業界リポート複数 |
| Agent Teams（実験的） | 公式ドキュメント |

---

### 非公開と確認されているもの

| 内容 | 状況 |
|---|---|
| Anthropic 内部の CLAUDE.md ファイルの実際の内容 | Boris は原則は共有するが、ファイル本体は非公開 |
| Boris Cherny が使う全カスタムコマンド | 一部（/grill, /test-and-fix 等）は公開、全量は非公開 |
| Karpathy の AutoResearch 実装詳細 | 概念は公開、コードは非公開 |
| Anthropic 内部の評価フレームワーク（evals） | 存在は明示、詳細は非公開 |
| AutoDream 機能の詳細 | リリース済みだがドキュメント不足 |

---

### 推測（状況証拠から推定）

| 推測 | 根拠 |
|---|---|
| Anthropic 内部では Claude-on-Claude 開発（自己参照的開発）を体系化している | claude-code-action の CLAUDE.md の品質、および Boris が「Claude Code 自体を Claude Code で書いている」と示唆 |
| Evals-Driven Development: 機能追加前に評価セットを先に定義する | Anthropic の evals 文化 + TDD との親和性から推測 |
| 内部にプロンプトテンプレートライブラリが存在する | 公開コマンドの品質水準から、体系的なプロンプトエンジニアリングの蓄積が見える |
| CLAUDE.md を複数層に分けた「遅延ロード記憶」を設計している | `.claude/rules/` の `paths:` フロントマターの活用から推測 |
| Ultraplan の内部に「Mythos」というマルチエージェント計画基盤がある | Medium 記事1本のみ（Anthropic 未確認） |

---

## デモと現実のギャップ（重要）

Anthropic が公式に認めているもの:
- **CLAUDE.md の遵守率は約 70%**（残り 30% は無視される）。100% 守らせたいルールはフックで実装する
- Agent Teams は出荷済みだが「実験的」でまだ本番向けではない

コミュニティが指摘しているデモとのギャップ:
- **トークンコスト管理**はデモでは見えないが実際の最重要制約（企業が数ヶ月で予算を使い果たす事例あり）
- **10〜15 並行セッション管理の精神的負荷**は公式資料には出てこない
- 「流れるような自律エージェント」のデモは理想像であり、実際はコンテキスト管理に多くの手間がかかる

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

コマンドフックはツール権限の確認外で自動実行される。`npx`・`npm`・`python`・`node` などの
パッケージランナーやインタープリタは登録せず、固定された読み取り専用チェックや保護処理に限定する。

```json
{
  "hooks": {
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
