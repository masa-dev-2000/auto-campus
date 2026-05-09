# auto-campus

このファイルを読んだ証明として、最初の返答で必ず「auto-campus セッション開始」と述べること。

## プロジェクト概要

リアルタイム会議サポートシステム。会話を文字起こしし、情報を視覚化・補強・リサーチする。

## オーナーの目的（3つ）

1. Claude Code (CC) のノウハウを学習しながら開発する
2. 開発プロジェクトの進め方のノウハウを学習する
3. ツールを実際に作る

## ドキュメント構造

```
docs/
├── cc-knowhow.md        # Claude Code のノウハウ集
├── dev-methodology.md   # 開発プロジェクトの進め方
└── project/
    ├── overview.md      # 要件・機能定義
    └── phases.md        # フェーズ・タスク一覧
```

## 開発スタック

- STT: Deepgram Nova-3（WebSocketストリーミング）
- VAD: Silero VAD
- LLM（即時）: Claude Haiku 4.5
- LLM（リサーチ・整理）: Claude Sonnet 4.6
- リサーチ: Perplexity API
- バックエンド: Python / FastAPI（uv で管理）
- フロントエンド: React / Vite（Tauri 内に組み込み）
- デスクトップシェル: Tauri（Rust）— macOS / Windows / Linux クロスプラットフォーム
- 音声キャプチャ: 対面マイクのみ（Phase 0）
- 注意: macOS=WebKit / Windows=WebView2(Chromium) / Linux=WebKitGTK でエンジンが異なる

## コマンド

（Phase 0 完了後に追記）

## 規約

- ユーザーへのアクション・確認事項はチャットではなくドキュメントに書く
- コメントは日本語で書く
- コミットは1ファイル1コミットを基本とする
- any 型禁止
- テスト未実行でコミットしない（テスト整備後）

## フェーズ状況

現在: Phase 0（環境構築・PoC）開始前
詳細: docs/project/phases.md を参照
