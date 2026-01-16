# Multi-Agent tmux

tmux上で複数のAIエージェント（Claude Code、Gemini CLI、Codex）を効率的に操作するためのスキル集。

## 概要

このプロジェクトは、tmuxを使って複数のAIエージェントを並列または単独で操作し、質問に答えてもらうためのノウハウとスキルをまとめたものです。

## スキル一覧

### 1. tmux-multi-agent
複数のAIエージェント（Claude Code、Gemini CLI、Codex）を並列で動かし、同じ質問に対する回答を比較するためのスキル。

**用途:**
- 複数の視点から回答を得たい場合
- 情報の正確性を複数のAIで確認したい場合
- 各AIの特性を比較したい場合

**ドキュメント:**
- [SKILL.md](skills/tmux-multi-agent/SKILL.md)
- [reference.md](skills/tmux-multi-agent/reference.md)

### 2. tmux-single-agent
1つのAIエージェント（Claude Code、Gemini CLI、Codexのいずれか）を使って質問に答えてもらうためのシンプルなスキル。

**用途:**
- 特定のエージェントに質問したい場合
- ファイル参照が必要な場合（Claude Codeのみ）
- シンプルに1つのAIと対話したい場合

**ドキュメント:**
- [SKILL.md](skills/tmux-single-agent/SKILL.md)
- [examples.md](skills/tmux-single-agent/examples.md)

### 3. troubleshooting
tmux操作やエージェント起動時のトラブルシューティング。

**ドキュメント:**
- [SKILL.md](skills/troubleshooting/SKILL.md)

## クイックスタート

### シングルエージェントで質問（Claude Code）

```bash
# ペイン作成
tmux split-window -h -p 50

# Claude Code起動
tmux send-keys -t 1 'claude' C-m

# 質問送信
tmux send-keys -t 1 'Pythonのリスト内包表記について教えてください' C-m

# 回答確認
tmux capture-pane -t 1 -p | tail -12
```

### マルチエージェントで並列質問

```bash
# レイアウト構築（左メイン + 右3分割）
tmux split-window -h -p 50
tmux split-window -t 1 -v -p 67
tmux split-window -t 2 -v -p 50

# 各エージェント起動
tmux send-keys -t 1 'claude' C-m
tmux send-keys -t 2 'gemini -y' C-m
tmux send-keys -t 3 'codex --full-auto' C-m

# 同じ質問を全エージェントに送信
# （個別にコマンド実行）
```

## 各AIエージェントの特徴

| エージェント | 得意分野 | 備考 |
|-------------|---------|------|
| **Claude Code** | コード生成・編集、ファイル参照 | `@ファイル名`でファイル参照可能 |
| **Gemini CLI** | 最新情報の検索、マルチモーダル | Google検索と連携 |
| **Codex** | OpenAI GPT-4ベース | 別の視点からの回答 |

## プロンプト

### Professor Synapse
マルチエージェント操作時に使用する、エージェント召喚型のプロンプト。

**場所:** [prompts/professor_synapse.md](prompts/professor_synapse.md)

## セキュリティ注意事項

**チェーンコマンドは使用禁止**

```bash
# ❌ 危険
tmux split-window -h && tmux send-keys 'claude'

# ✅ 安全
tmux split-window -h
tmux send-keys 'claude'
```

理由: `&&` や `;` を許可すると、悪意あるコマンドが混入する可能性があります。

## 必要な環境

- tmux
- Claude Code CLI (`claude`)
- Gemini CLI (`gemini`)
- OpenAI Codex CLI (`codex`)

## ディレクトリ構造

```
multi-agent-tmux/
├── README.md                          # このファイル
├── prompts/
│   └── professor_synapse.md           # Professor Synapseプロンプト
└── skills/
    ├── tmux-multi-agent/              # マルチエージェントスキル
    │   ├── SKILL.md
    │   └── reference.md
    ├── tmux-single-agent/             # シングルエージェントスキル
    │   ├── SKILL.md
    │   └── examples.md
    └── troubleshooting/               # トラブルシューティング
        └── SKILL.md
```

## ライセンス

MIT

## 貢献

プルリクエストやイシューを歓迎します。
