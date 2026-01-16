# cc-marketplace

このリポジトリは、Claude Codeのカスタムプラグインを収集したマーケットプレイスです。

## プラグイン一覧

現在準備中です。

## インストール方法

このマーケットプレイスからプラグインをインストールするには：

```bash
# マーケットプレイスをマーケットプレイスリストに追加
claude /plugin add-marketplace https://github.com/YOUR_USERNAME/cc-marketplace

# 特定のプラグインをインストール
claude /plugin install <plugin-name>
```

または、個別のプラグインディレクトリを使用してテスト：

```bash
# リポジトリをクローン
git clone https://github.com/YOUR_USERNAME/cc-marketplace

# 特定のプラグインをテスト
claude --plugin-dir ./cc-marketplace/<plugin-name>
```

## プラグイン開発について

各プラグインは独立したディレクトリとして管理されます。プラグインの作成方法については、[Claude Code公式ドキュメント](https://code.claude.com/docs/create-plugins)を参照してください。

## ライセンス

各プラグインは独自のライセンスを持つ場合があります。詳細は各プラグインのディレクトリを確認してください。
