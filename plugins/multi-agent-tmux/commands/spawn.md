---
description: tmuxの新しいペインでAIエージェント（Claude/Gemini/Codex）を起動する
---

# Spawn Agent

新しいtmuxペインを作成し、AIエージェントを起動します。

## 引数
- `$1`: CLI種類（claude / gemini / codex）
- `$2`（オプション）: エージェント名（識別用ラベル）

## 対応CLI

| CLI | コマンド | 説明 |
|-----|---------|------|
| claude | `claude` | Claude Code |
| gemini | `gemini -y` | Gemini CLI（自動承認モード） |
| codex | `codex --full-auto` | OpenAI Codex（フルオートモード） |

## 手順

1. 現在tmux内で実行されているか確認
2. CLI種類に応じたコマンドでペインを分割・起動:

```bash
# Claude Code
tmux split-window -h 'claude'

# Gemini CLI
tmux split-window -h 'gemini -y'

# Codex
tmux split-window -h 'codex --full-auto'
```

3. フォーカスを元のペインに戻す:
```bash
tmux select-pane -t 0
```

4. 新しいペイン番号をユーザーに報告

## 注意事項
- セキュリティのため、コマンドはチェーン（&&）せず個別に実行すること
- tmux内でないとペイン分割はできない
