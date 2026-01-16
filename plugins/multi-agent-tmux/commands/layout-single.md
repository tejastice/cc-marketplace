---
description: シングルエージェント用レイアウト（左メイン + 右1分割）を構築する
---

# Layout Single

シングルエージェント用のシンプルなレイアウトを構築します。

## レイアウト構成

```
┌──────────────────────┬──────────────────────┐
│                      │                      │
│                      │                      │
│                      │                      │
│ ペイン0: メイン      │ ペイン1: AI エージェント │
│                      │                      │
│                      │                      │
│                      │                      │
└──────────────────────┴──────────────────────┘
```

## 手順

1. 現在tmux内か確認
2. 以下のコマンドを実行してレイアウトを構築:

```bash
# レイアウト構築（左右2分割、50%ずつ）
tmux split-window -h -p 50
tmux select-pane -t 0
```

3. ペイン1でCLIを起動する案内を表示:

```bash
# Claude Code
tmux send-keys -t 1 'claude' C-m

# または Gemini CLI（自動承認モード）
tmux send-keys -t 1 'gemini -y' C-m

# または Codex（フルオートモード）
tmux send-keys -t 1 'codex --full-auto' C-m
```

## 使用例

### Claude Codeでファイル参照しながら質問

```bash
# レイアウト構築
tmux split-window -h -p 50
tmux select-pane -t 0

# Claude Code起動
tmux send-keys -t 1 'claude' C-m

# 質問送信（ファイル参照可能）
tmux send-keys -t 1 '@README.md このファイルの内容を要約してください' C-m

# 完了確認
tmux capture-pane -t 1 -p | tail -12
```

## 注意事項

- シンプルな1対1の対話に最適
- ファイル参照が必要な場合はClaude Codeを推奨
- 複数AIで比較したい場合は `/multi-agent-tmux:layout-multi` を使用
