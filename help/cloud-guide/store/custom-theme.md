---
title: カスタムテーマ
description: クラウドインフラストラクチャ上のAdobe Commerceにカスタムテーマをインストールする方法を説明します。
feature: Cloud, Themes
exl-id: f08134ab-daea-471d-a927-02531d36a809
source-git-commit: bb7a866b1896a8a43d01ad3f83dc655bcf383374
workflow-type: tm+mt
source-wordcount: '295'
ht-degree: 0%

---

# カスタムテーマ

プロジェクト内の 1 つまたはすべてのストアとサイトに使用する 1 つまたは複数のテーマをインストールできます。 テーマには、ストアを完全にデザインするために、画像、フォント、CSS、JavaScript、PHP など、複数の静的ファイルが含まれます。 テーマを追加するには、コードをファイルシステムに抽出するか、Composer を使用します。

## テーマを手動でインストール

テーマを手動でインストールするには、テーマのコードを圧縮されたアーカイブまたは次のようなディレクトリ構造にする必要があります。

```text
<VendorName>
  ├── composer.json
      ├── etc
      │   └── view.xml
      ├── media
      ├── registration.php
      ├── theme.xml
      └── web
          ├── css
          │   └── source
          ├── fonts
          ├── images
          └── js
```

**テーマを手動でインストール**:

1. の下にテーマのコードをコピーします。 `<Project root dir>/app/design/frontend` ストアフロントのテーマの場合はまたは `<Project root dir>/app/design/adminhtml` 管理テーマの場合。 最上位ディレクトリがであることを確認 `<VendorName>`。そうでない場合、テーマが正しくインストールされません。

   ```bash
   cp -r ExampleTheme <project-root>/app/design/frontend
   ```

1. 正しい場所にコピーされたテーマを確認します。

   * ストアフロントのテーマ： `ls <project-root>/app/design/frontend`
   * 管理テーマ： `ls <project-root>/app/design/adminhtml`

   次に例を示します。

   ExampleTheme Adobe Commerce

1. ファイルの追加とコミット

   ```bash
   git add -A && git commit -m "Add theme"
   ```

1. ファイルをブランチにプッシュします。

   ```bash
   git push origin <branch name>
   ```

1. デプロイメントが完了するまで待ちます。
1. 管理者にログインします。
1. クリック **コンテンツ** > デザイン > **テーマ**.

   テーマが右側のパネルに表示されます。

## Composer を使用したテーマのインストール

Composer を使用してテーマをインストールする方法は、Composer を使用して他の拡張機能をインストールする方法と同様です。 参照： [モジュールのインストール、管理、アップグレード](extensions.md) を参照してください。

Composer を使用してテーマをインストールするには：

1. テーマをCommerce Marketplaceから購入します。
1. テーマのコンポーザー名を取得します。
1. Adobe Commerceのルートディレクトリに移動して、コマンドを入力します。

   ```bash
   composer require <vendor>/<name>:<version>
   ```

   以下に例を挙げます。

   ```bash
   composer require zero1/theme-fashionista-theme:1.0.0
   ```

1. 依存関係が更新されるのを待ちます。
1. 次のコマンドを入力します。

   ```bash
   git add -A && git commit -m "Add theme"
   ```

   ```bash
   git push origin <branch name>
   ```

1. 管理者にログインします。
1. クリック **コンテンツ** > デザイン > **テーマ**.

   テーマが右側のパネルに表示されます。

## 複数のテーマ

ロケールごとに異なるテーマを使用するなど、複数のテーマを使用する場合、 `SCD_MATRIX` テーマのデプロイメントをカスタマイズするための環境変数。 を参照してください。 [ビルド](../environment/variables-build.md#scd_matrix) または [deploy](../environment/variables-deploy.md#scd_matrix) のステージ [環境設定](../environment/configure-env-yaml.md).
