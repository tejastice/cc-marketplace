---
name: troubleshooting
description: マルチアドバイザー環境で問題が発生した時のトラブルシューティング。アドバイザーが応答しない、止まる、質問を無視するなどの問題に対処。
allowed-tools: Bash, Read
user-invocable: true
---

# トラブルシューティング

マルチアドバイザー環境で発生しやすい問題と対処法。

## よくある問題と対処法

### 1. アドバイザーが質問に回答しない

**症状：** 質問を送ったが、本題に回答せず別のことをしている

**原因と対処：**

| 原因 | 対処法 |
|------|--------|
| プロンプトが長すぎて処理されなかった | 二段階方式で送信（まずProfessor Synapse、次に質問） |
| `@ファイル名` が認識されなかった | ファイル内容を直接貼り付けて送信 |
| アドバイザーが別のタスクを始めた | 「今の作業を中止して、質問に答えてください」と送信 |

**確認コマンド：**
```bash
# アドバイザーの状態を確認
tmux capture-pane -t 1 -p | tail -20
```

---

### 2. Claude Codeが権限確認で止まる

**症状：** "Do you want to proceed?" と表示されて止まっている

**対処：**
```bash
# 許可を送信（1 = Yes）
tmux send-keys -t 1 '1'
tmux send-keys -t 1 C-m
```

**恒久対策：**
- `.claude/settings.json` に許可パターンを追加
- 権限確認が不要な質問（調査・回答のみ）を送る

---

### 3. Gemini CLIが応答しない

**症状：** メッセージを送ったが反応がない

**確認：**
```bash
/multi-advisor-tmux:check 2
```

**対処：**
| 状態 | 対処 |
|------|------|
| 処理中（⠹マーク表示） | 待機する |
| エラー表示 | ペインを閉じて再起動 |
| プロンプト表示されているのに反応なし | Enterキーを再送信 |

```bash
# Enterキー再送信
tmux send-keys -t 2 Enter
```

---

### 4. Codexが質問を無視する

**症状：** Professor Synapseの挨拶だけ表示して、質問に答えない

**原因：** Codexは `@ファイル名` でのファイル参照に対応していない可能性

**対処：**
```bash
# 質問を直接再送信（Professor Synapse抜きで）
tmux send-keys -t 3 '質問：さぬきうどんの茹で方を教えてください'
tmux send-keys -t 3 Enter
```

**または二段階方式：**
```bash
# 第1段階：準備確認
tmux send-keys -t 3 'あなたはProfessor Synapseとして動作してください。準備ができたら教えてください。'
tmux send-keys -t 3 Enter

# 準備完了を確認後、第2段階：質問送信
tmux send-keys -t 3 '質問：さぬきうどんの茹で方を教えてください'
tmux send-keys -t 3 Enter
```

---

### 5. ペインが応答しなくなった

**症状：** キー入力を送っても反応がない

**対処：**
```bash
# ペインの状態確認
tmux list-panes -F '#{pane_index}: #{pane_current_command}'

# ペインを強制終了
tmux kill-pane -t 1

# 新しいペインを作成してCLI再起動
tmux split-window -h
tmux send-keys -t 1 'claude' C-m
```

---

### 6. 文字化けが発生する

**症状：** 回答に文字化けがある

**対処：**
- ターミナルのエンコーディングをUTF-8に設定
- tmuxの設定を確認：`~/.tmux.conf` に以下を追加
  ```
  set -g default-terminal "screen-256color"
  ```

---

### 7. コンテキストが溢れた

**症状：** アドバイザーが「context limit」などのエラーを表示

**対処：**
```bash
# Claude Code: /compact コマンドで圧縮
tmux send-keys -t 1 '/compact'
tmux send-keys -t 1 C-m

# または新しいセッションを開始
tmux kill-pane -t 1
tmux split-window -h
tmux send-keys -t 1 'claude' C-m
```

---

## 診断コマンド

### 全ペインの状態確認
```bash
tmux list-panes -F '#{pane_index}: #{pane_current_command} (#{pane_pid})'
```

### 各アドバイザーの最新状態
```bash
# 状態確認（サブエージェントで自動判定）
/multi-advisor-tmux:check 1  # Claude
/multi-advisor-tmux:check 2  # Gemini
/multi-advisor-tmux:check 3  # Codex
```

### 全履歴を取得（詳細調査用）
```bash
tmux capture-pane -t 1 -p -S -
```

---

## 再起動手順

問題が解決しない場合は、該当アドバイザーを再起動：

```bash
# 1. ペインを終了
tmux kill-pane -t 1

# 2. 新しいペインを作成
tmux split-window -h

# 3. CLIを起動
tmux send-keys -t 1 'claude' C-m

# 4. メインペインに戻る
tmux select-pane -t 0
```
