# tmux コマンドリファレンス

## ペイン操作

| コマンド | 説明 |
|----------|------|
| `tmux split-window -h` | 横分割（右に新ペイン） |
| `tmux split-window -v` | 縦分割（下に新ペイン） |
| `tmux split-window -h 'claude'` | 横分割してコマンド実行 |
| `tmux select-pane -t N` | ペインNを選択 |
| `tmux send-keys -t N 'cmd'` | ペインNにキー送信 |
| `tmux send-keys -t N C-m` | ペインNにEnter送信（Claude用） |
| `tmux send-keys -t N Enter` | ペインNにEnter送信（Gemini/Codex用） |
| `tmux capture-pane -t N -p` | ペインNの内容取得 |
| `tmux capture-pane -t N -p -S -` | ペインNの全履歴取得 |
| `tmux kill-pane -t N` | ペインNを終了 |
| `tmux list-panes` | ペイン一覧表示 |
| `tmux list-panes -F '#{pane_index}: #{pane_current_command}'` | フォーマット指定で一覧 |

## セッション操作

| コマンド | 説明 |
|----------|------|
| `tmux new -s NAME` | 新規セッション作成 |
| `tmux attach -t NAME` | セッションに接続 |
| `tmux detach` | セッションから離脱 |
| `tmux kill-session -t NAME` | セッション削除 |
| `tmux ls` | セッション一覧 |

## 推奨レイアウト構築

### 左メイン + 右3分割（マルチエージェント用）

```
┌──────────────────────┬──────────────────────┐
│                      │ ペイン1: Claude Code │
│                      ├──────────────────────┤
│ ペイン0: メイン      │ ペイン2: Gemini CLI  │
│                      ├──────────────────────┤
│                      │ ペイン3: Codex       │
└──────────────────────┴──────────────────────┘
```

**推奨：コマンドを使用**
```bash
/multi-agent-tmux:layout-multi
```

**または手動で構築:**
```bash
tmux split-window -h -p 50 \; split-window -t 1 -v -p 67 \; split-window -t 2 -v -p 50 \; select-pane -t 0
```

### 左右2分割（シングルエージェント用）

```
┌──────────────────────┬──────────────────────┐
│                      │                      │
│ ペイン0: メイン      │ ペイン1: エージェント│
│                      │                      │
└──────────────────────┴──────────────────────┘
```

**推奨：コマンドを使用**
```bash
/multi-agent-tmux:layout-single
```

**または手動で構築:**
```bash
tmux split-window -h -p 50
tmux select-pane -t 0
```

## CLI起動オプション

### Claude Code
```bash
claude                                    # 通常起動
claude --settings /path/to/settings.json  # 設定ファイル指定
claude -p "質問"                          # 非対話モード
```

### Gemini CLI
```bash
gemini      # 通常起動
gemini -y   # 自動承認モード
```

### OpenAI Codex
```bash
codex                          # 通常起動
codex --approval-mode full-auto # フルオートモード
```

## capture-pane オプション

| オプション | 説明 |
|-----------|------|
| `-t N` | 対象ペイン番号 |
| `-p` | 標準出力に出力 |
| `-S -` | 履歴の最初から |
| `-S -30` | 最後の30行 |
| `-E -` | 履歴の最後まで |

## 注意事項

1. **ペイン番号は0から始まる**（左/上が0、右/下が1...）
2. **tmux内でないとペイン分割不可**
3. **フォーカス移動**: サブエージェント起動後は `tmux select-pane -t 0` でメインに戻す
