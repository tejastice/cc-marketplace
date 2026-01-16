# tmux-single-agent & multi-advisor 使用例

シングルアドバイザー、マルチアドバイザー共通の使用例集です。

## 例1: Claude Codeで技術的な質問（Professor Synapse使用）

```bash
# ペインを作成してClaude Codeを起動
tmux split-window -h -p 50
tmux send-keys -t 1 'claude'
tmux send-keys -t 1 C-m

# 起動を待つ
sleep 1

# Professor Synapseプロンプトを含む質問を送信
tmux send-keys -t 1 '@professor_synapse.md を読んで、このプロンプトに従って動作してください。

追加ルール：
- 正確な情報を得るため、必要に応じてネット検索を活用してください
- 情報が不足している場合は質問してください。私が答えます
- わからないことは正直に「わからない」と伝えてください

背景：マイクロサービスアーキテクチャへの移行を検討しています。

質問：Pythonで非同期処理を実装する際のベストプラクティスを教えてください。asyncioとthreadingの使い分けも含めて。'
tmux send-keys -t 1 C-m

# 状態確認
/multi-advisor-tmux:check 1  # → "Processing" または "Ready"

# Ready になったら回答取得（response-extractorエージェントに依頼）
# 「ペイン1の回答を取得して」と指示
```

## 例2: Gemini CLIで最新情報を調査

```bash
# ペインを作成してGemini CLIを起動
tmux split-window -h -p 50
tmux send-keys -t 1 'gemini -y'
tmux send-keys -t 1 Enter

# 起動を待つ
sleep 1

# ネット検索を含む質問
tmux send-keys -t 1 '追加ルール：
- 必ずネット検索を活用して最新情報を取得してください

背景：Next.js 15の新機能について調べています

質問：Next.js 15の主な新機能と変更点を教えてください'
tmux send-keys -t 1 Enter

# 完了確認
/multi-advisor-tmux:check 1
```

## 例3: Codexでコードレビュー

```bash
# ペインを作成してCodexを起動
tmux split-window -h -p 50
tmux send-keys -t 1 'codex --full-auto'
tmux send-keys -t 1 Enter

# 起動を待つ
sleep 1

# コードレビューの依頼
tmux send-keys -t 1 '以下のPythonコードをレビューして、改善点を指摘してください：

def calc(a, b):
    return a + b

result = calc(10, 20)
print(result)'
tmux send-keys -t 1 Enter

# 完了確認
/multi-advisor-tmux:check 1
```

## 例4: Claude Codeでファイル参照しながら質問

```bash
# ペインを作成してClaude Codeを起動
tmux split-window -h -p 50
tmux send-keys -t 1 'claude'
tmux send-keys -t 1 C-m

# 起動を待つ
sleep 1

# @ファイル参照を使った質問（Claude Codeのみ）
tmux send-keys -t 1 '@package.json を読んで、このプロジェクトで使用している主要な依存ライブラリとそのバージョンを教えてください'
tmux send-keys -t 1 C-m
```

## 例5: 追加質問のやりとり

```bash
# 初回質問
tmux send-keys -t 1 'ReactのuseEffectフックについて教えてください'
tmux send-keys -t 1 C-m

# 完了を待つ
sleep 1

# 回答を確認
/multi-advisor-tmux:check 1

# 追加質問
tmux send-keys -t 1 'クリーンアップ関数の具体的な使用例を教えてください'
tmux send-keys -t 1 C-m

# 再度回答を確認
/multi-advisor-tmux:check 1
```

## 例6: 既存のペインを再利用

```bash
# すでに起動しているペインの番号を確認
tmux list-panes -t agent -F "#{pane_index}: #{pane_current_command}"

# 例：ペイン2にClaude Codeが起動している場合
# そのペインに質問を送信
tmux send-keys -t 2 'TypeScriptの型安全性について説明してください'
tmux send-keys -t 2 C-m

# 完了確認
/multi-advisor-tmux:check 2
```

## ワンライナーでの実行

### Claude Codeで素早く質問

```bash
tmux split-window -h -p 50
tmux send-keys -t 1 'claude'
tmux send-keys -t 1 C-m
sleep 1
tmux send-keys -t 1 'Dockerコンテナとは何か、初心者向けに説明してください' C-m
```

### Gemini CLIで最新ニュースを取得

```bash
tmux split-window -h -p 50
tmux send-keys -t 1 'gemini -y' Enter
sleep 1
tmux send-keys -t 1 'ネット検索を使って、2026年1月のAI業界の最新ニュースを教えてください' Enter
```

## トラブルシューティング

### ペインが応答しない

```bash
# ペインの状態を確認
tmux capture-pane -t 1 -p | tail -20

# プロセスを確認
tmux list-panes -t agent -F "#{pane_index}: #{pane_current_command}"

# 必要であればペインを閉じて再作成
tmux kill-pane -t 1
```

### CLIが起動しない

```bash
# コマンドが存在するか確認
which claude
which gemini
which codex

# パスが通っていない場合はフルパスで指定
tmux send-keys -t 1 '/path/to/claude' C-m
```

## ベストプラクティス

1. **Professor Synapseプロンプトを使う**: より高品質な回答を得られる
2. **質問には背景情報を含める**: アドバイザーが文脈を理解しやすくなる
3. **完了確認は/checkコマンドを使う**: 手動キャプチャより正確
4. **ネット検索が必要な場合は明示する**: 追加ルールで「ネット検索を活用してください」と指示
5. **追加質問で深掘りする**: 1回で完璧な回答を求めず、対話的に進める
6. **response-extractorで回答取得**: クリーンな回答のみを取得してコンテキスト節約
