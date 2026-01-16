---
description: 指定したペインの状態を簡潔に確認する
---

# Check

指定したペイン番号のAIエージェントの状態を簡潔に確認します。

## 引数
- `$1`: ペイン番号（例：1, 2, 3）

## 手順

1. pane-status-checkerエージェントを使用して状態を判定
2. 状態を1行で報告（Ready / Processing / Awaiting Permission / Error）

## 使用例

```bash
# ペイン1の状態確認
/multi-advisor-tmux:check 1
```

## 出力例

```
Ready
```

または

```
Processing
```

または

```
Awaiting Permission
```

または

```
Error: command not found
```

## 複数ペインの状態を一括確認

```bash
# 各ペインの状態を確認
/multi-advisor-tmux:check 1
/multi-advisor-tmux:check 2
/multi-advisor-tmux:check 3
```

## 活用シーン

- **完了検知**: 処理が終わったか確認
- **並列処理監視**: 複数エージェントの進捗確認
- **許可待ち検知**: ユーザー操作が必要かチェック
- **エラー検知**: 異常終了していないか確認

## 詳細な回答が必要な場合

状態だけでなく回答内容も必要な場合は `/multi-advisor-tmux:capture` を使用してください。
