---
description: 指定したtmuxペインの完了確認または回答取得を行う
---

# Capture

指定したペインの完了確認、または回答内容の取得を行います。

## 用途別の使い方

### 完了確認（12行で十分）
```bash
tmux capture-pane -t 1 -p | tail -12
```
プロンプト（❯, Type your message, >）が表示されていれば処理完了。

### 回答内容の取得
response-extractorエージェントを使用する。
「ペイン1の回答を取得して」と指示する。

## 実行例

```bash
# 完了確認
tmux capture-pane -t 1 -p | tail -12

# 履歴全体を取得（必要な場合のみ）
tmux capture-pane -t 1 -p -S -
```

## コンテキスト節約のコツ

- **完了確認は12行**: `tail -12` で十分
- **回答取得はresponse-extractor**: サブエージェントに抽出を任せる
- **全履歴は本当に必要な時だけ**: `-S -` は控えめに
