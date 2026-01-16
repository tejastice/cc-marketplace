# Multi-Advisor tmux

tmux上で複数のAIアドバイザー（Claude Code、Gemini CLI、Codex）を効率的に操作し、複数の視点からアドバイスを得るためのClaude Codeプラグイン。

## 特徴

- **マルチアドバイザー対応**: 複数のAIを並列で動かし、回答を比較
- **自動状態検知**: 処理中/完了/エラーを自動判定
- **コンテキスト節約**: サブエージェント（Haikuモデル）で効率的に処理
- **セキュアな設計**: コマンドインジェクションを防ぐ安全な実装

## インストール

### 前提条件

このプラグインを使用するには、以下のソフトウェアが必要です：

#### 必須

- **tmux** - ペイン管理に必要
  ```bash
  # macOS
  brew install tmux

  # Ubuntu/Debian
  sudo apt install tmux
  ```

#### AIアドバイザーCLI（少なくとも1つ必要）

このプラグインは複数のAI CLIツールをサポートしています。使用したいものをインストールしてください：

- **Claude Code CLI** (`claude`)
  - コード生成・編集、ファイル参照（`@ファイル名`）が得意
  - インストール方法: [Claude Code公式ドキュメント](https://github.com/anthropics/claude-code)を参照

- **Gemini CLI** (`gemini`)
  - Google検索、最新情報、マルチモーダル処理が得意
  - インストール方法: [Gemini CLI公式リポジトリ](https://github.com/google/generative-ai-cli)を参照

- **OpenAI Codex CLI** (`codex`)
  - GPT-4ベース、別の視点からの回答が得意
  - インストール方法: OpenAI公式ドキュメントを参照

**注意**: 各CLIツールのインストール方法は公式ドキュメントをご確認ください。インストール手順やコマンド名は変更される可能性があります。

### プラグインのインストール

#### マーケットプレイスから

```bash
# マーケットプレイスを追加
claude /plugin marketplace add tejastice/cc-marketplace

# プラグインをインストール
claude /plugin install multi-advisor-tmux@cc-marketplace
```

### ローカル開発

```bash
# リポジトリをクローン
git clone https://github.com/tejastice/cc-marketplace

# プラグインディレクトリを指定して起動
claude --plugin-dir ./cc-marketplace/plugins/multi-advisor-tmux
```

## 基本的な使い方

### シングルアドバイザー（1対1の対話）

```bash
# 1. レイアウト構築
/multi-advisor-tmux:layout-single

# 2. ペイン1にClaude Codeを起動（ガイドに従う）

# 3. 質問を送信
/multi-advisor-tmux:ask 1 @README.md このファイルの内容を要約してください

# 4. 状態を確認
/multi-advisor-tmux:check 1  # → "Ready" または "Processing"

# 5. Ready になったら回答を取得
「ペイン1の回答を取得して」
```

### マルチアドバイザー（並列比較）

```bash
# 1. レイアウト構築
/multi-advisor-tmux:layout-multi

# 2. 各ペインにCLI起動（ガイドに従う）
# ペイン1: Claude Code
# ペイン2: Gemini CLI
# ペイン3: Codex

# 3. 各ペインに同じ質問を送信
/multi-advisor-tmux:ask 1 Pythonの非同期処理について教えてください
/multi-advisor-tmux:ask 2 Pythonの非同期処理について教えてください
/multi-advisor-tmux:ask 3 Pythonの非同期処理について教えてください

# 4. 全ペインの状態を確認
/multi-advisor-tmux:status

# 5. 各ペインが Ready になったら回答を取得
「ペイン1の回答を取得して」
「ペイン2の回答を取得して」
「ペイン3の回答を取得して」
```

## コマンド一覧

### レイアウト構築
| コマンド | 説明 |
|---------|------|
| `/multi-advisor-tmux:layout-single` | シングルアドバイザー用レイアウト（左右2分割） |
| `/multi-advisor-tmux:layout-multi` | マルチアドバイザー用レイアウト（左メイン + 右3分割） |

### アドバイザー操作
| コマンド | 説明 |
|---------|------|
| `/multi-advisor-tmux:spawn <cli> [name]` | 新しいペインでAIアドバイザーを起動 |
| `/multi-advisor-tmux:ask <pane> <message>` | 指定ペインに質問を送信 |
| `/multi-advisor-tmux:check <pane>` | ペインの状態を簡潔に確認 |
| `/multi-advisor-tmux:capture <pane>` | 完了確認または回答取得 |

### 全体管理
| コマンド | 説明 |
|---------|------|
| `/multi-advisor-tmux:status` | 全ペインの状態を一覧表示 |

## サブエージェント

### pane-status-checker（Haikuモデル）

ペインの状態を自動判定する専門エージェント。

**判定できる状態:**
- `Ready` - 入力待ち（処理完了）
- `Processing` - 処理中
- `Awaiting Permission` - ユーザー操作待ち
- `Error` - エラー発生

**判定方法:**
処理中表示（`(ctrl+c to interrupt)`, `• Working`, `(esc to cancel)`など）の有無で判定。
プロンプト文字（❯, ›, >）は処理中でも表示されるため使用しない。

### response-extractor（Haikuモデル）

ペインからAI回答を抽出する専門エージェント。

**機能:**
- ノイズ除去（プロンプト、ステータスバー、ツール実行ログなど）
- クリーンな回答テキストのみを返す
- コンテキスト節約

## 各AIアドバイザーの特徴

| アドバイザー | 起動コマンド | 処理中の表示 | 得意分野 |
|-------------|------------|------------|---------|
| **Claude Code** | `claude` | `(ctrl+c to interrupt)`, `✻ <text>…` | コード生成・編集、ファイル参照（`@ファイル名`） |
| **Gemini CLI** | `gemini -y` | `(esc to cancel)`, スピナー | Google検索、最新情報、マルチモーダル |
| **Codex** | `codex --full-auto` | `• Working`, `(esc to interrupt)` | GPT-4ベース、別の視点からの回答 |

**重要**: プロンプト文字（❯, ›, >）の有無だけでは完了判定できない。必ず`/check`コマンドを使用すること。

## スキル

### 1. tmux-multi-advisor
複数のAIアドバイザーを並列で動かし、複数の視点から回答を得るスキル。

**ドキュメント**: [skills/tmux-multi-advisor/SKILL.md](skills/tmux-multi-advisor/SKILL.md)

### 2. tmux-single-advisor
1つのAIアドバイザーと対話するシンプルなスキル。

**ドキュメント**: [skills/tmux-single-advisor/SKILL.md](skills/tmux-single-advisor/SKILL.md)

### 3. troubleshooting
tmux操作やアドバイザー起動時のトラブルシューティング。

**ドキュメント**: [skills/troubleshooting/SKILL.md](skills/troubleshooting/SKILL.md)

## 詳細ドキュメント

- **コマンドリファレンス**: [docs/command-reference.md](docs/command-reference.md) - tmuxコマンドの詳細
- **使用例**: [docs/examples.md](docs/examples.md) - 実践的な使用例集

## プロンプト

### Professor Synapse
エージェント召喚型の高度なプロンプト。マルチアドバイザー操作時に使用。

**場所**: [prompts/professor_synapse.md](prompts/professor_synapse.md)

## ワークフロー例

### マルチアドバイザーで技術調査

```
1. /multi-advisor-tmux:layout-multi でレイアウト構築
2. 3つのCLIを起動
3. Professor Synapseプロンプトを含む質問を送信
4. /multi-advisor-tmux:check 1-3 で状態を定期確認
5. 全て Ready になったら回答を取得・比較
6. 追加質問があれば /multi-advisor-tmux:ask で送信
7. 満足のいく回答が得られるまで繰り返し
```

## よくある質問

### Q: プロンプト（❯）が表示されているのに Processing と判定されるのはなぜ？

A: Claude Codeなどは処理中でもプロンプトを表示します。代わりに `(ctrl+c to interrupt)` などの処理中表示を見て判定しています。

### Q: 回答取得時にノイズが含まれる

A: `response-extractor`エージェントを使用してください。「ペイン1の回答を取得して」と指示するだけで、クリーンな回答のみを抽出します。

### Q: 複数アドバイザーの管理が面倒

A: `/multi-advisor-tmux:status`コマンドで全ペインの状態を一括確認できます。

### Q: ファイル参照したい

A: Claude Codeのみ対応しています。`/multi-advisor-tmux:ask 1 @README.md 要約して`のように使用します。

## セキュリティ

**チェーンコマンド禁止**

```bash
# ❌ 危険：コマンドインジェクションのリスク
tmux split-window -h && tmux send-keys 'claude'

# ✅ 安全：個別実行
tmux split-window -h
tmux send-keys 'claude'
```

すべてのtmuxコマンドは個別に実行され、`&&`や`;`によるチェーンは使用していません。

## ディレクトリ構造

```
multi-advisor-tmux/
├── README.md                          # このファイル
├── .claude-plugin/
│   └── plugin.json                    # プラグインマニフェスト
├── commands/                          # スラッシュコマンド
│   ├── layout-single.md
│   ├── layout-multi.md
│   ├── spawn.md
│   ├── ask.md
│   ├── check.md
│   ├── capture.md
│   └── status.md
├── agents/                            # サブエージェント
│   ├── pane-status-checker.md         # 状態判定（Haiku）
│   └── response-extractor.md          # 回答抽出（Haiku）
├── prompts/
│   └── professor_synapse.md           # Professor Synapseプロンプト
├── docs/                              # 詳細ドキュメント
│   ├── command-reference.md           # tmuxコマンドリファレンス
│   └── examples.md                    # 使用例集
└── skills/                            # Claude Codeスキル
    ├── tmux-multi-advisor/
    │   └── SKILL.md
    ├── tmux-single-advisor/
    │   └── SKILL.md
    └── troubleshooting/
        └── SKILL.md
```

## バージョン履歴

- **v1.4.0**: マルチエージェントスキルをマルチアドバイザースキルに名称変更
- **v1.3.1**: シングルアドバイザースキルにProfessor Synapse統合
- **v1.2.0**: スキルドキュメント更新、ドキュメント構造整理
- **v1.1.1**: 状態検知ロジック改善（実際のCLI挙動に基づく）
- **v1.1.0**: pane-status-checkerエージェント、/checkコマンド追加
- **v1.0.0**: 初回リリース

## ライセンス

MIT

## 貢献

プルリクエストやイシューを歓迎します。

**リポジトリ**: https://github.com/tejastice/cc-marketplace
