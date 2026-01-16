---
description: 全tmuxペインの状態を一覧表示する
---

# Status

現在のtmuxセッション内の全ペインの状態を確認します。

## 手順

1. `tmux list-panes -F` でペイン情報を取得
2. 各ペインの最新数行をキャプチャして状態を判断
3. 一覧形式で表示

## 実行コマンド

```bash
# ペイン一覧を取得
tmux list-panes -F '#{pane_index}: #{pane_current_command} (#{pane_width}x#{pane_height})'

# 各ペインの最新3行を確認
tmux capture-pane -t 0 -p | tail -3
tmux capture-pane -t 1 -p | tail -3
tmux capture-pane -t 2 -p | tail -3
tmux capture-pane -t 3 -p | tail -3
```

## 出力例

```
ペイン一覧:
  0: claude (80x40) - メイン
  1: claude (80x13) - 待機中
  2: gemini (80x13) - 処理中
  3: codex (80x13) - 待機中
```

## 完了判定のヒント

各CLIのプロンプトパターン:
- **Claude Code**: `❯` が表示されていれば待機中
- **Gemini CLI**: `Type your message` が表示されていれば待機中
- **Codex**: 行頭に `>` が表示されていれば待機中
