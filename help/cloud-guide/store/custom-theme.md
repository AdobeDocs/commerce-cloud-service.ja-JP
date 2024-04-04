---
title: カスタムテーマ
description: クラウドインフラストラクチャにAdobe Commerceを使用してカスタムテーマをインストールする方法を説明します。
feature: Cloud, Themes
exl-id: f08134ab-daea-471d-a927-02531d36a809
source-git-commit: bb7a866b1896a8a43d01ad3f83dc655bcf383374
workflow-type: tm+mt
source-wordcount: '295'
ht-degree: 0%

---

# カスタムテーマ

1 つまたは複数のテーマをインストールして、プロジェクト内の 1 つまたはすべてのストアとサイトに使用できます。 テーマには、画像、フォント、CSS、JavaScript、PHP など、ストアを完全に設計するための複数の静的ファイルが含まれます。 テーマを追加するには、コードをファイルシステムに抽出するか、Composer を使用します。

## テーマを手動でインストールする

テーマを手動でインストールするには、テーマのコードを圧縮されたアーカイブまたは次のようなディレクトリ構造で保存する必要があります。

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

**テーマを手動でインストールするには**:

1. テーマのコードを次の場所にコピーします。 `<Project root dir>/app/design/frontend` ストアフロントのテーマまたは `<Project root dir>/app/design/adminhtml` 管理テーマ用。 最上位ディレクトリが `<VendorName>`を使用しない場合、テーマは正しくインストールされません。

   ```bash
   cp -r ExampleTheme <project-root>/app/design/frontend
   ```

1. テーマを正しい場所にコピーしたことを確認します。

   * ストアフロントのテーマ： `ls <project-root>/app/design/frontend`
   * 管理テーマ： `ls <project-root>/app/design/adminhtml`

   次に例を示します。

   ExampleTheme Adobe Commerce

1. ファイルを追加してコミットします。

   ```bash
   git add -A && git commit -m "Add theme"
   ```

1. ブランチにファイルをプッシュします。

   ```bash
   git push origin <branch name>
   ```

1. デプロイメントが完了するまで待ちます。
1. 管理者にログインします。
1. クリック **コンテンツ** /デザイン/ **テーマ**.

   テーマが右側のウィンドウに表示されます。

## コンポーザーを使用したテーマのインストール

Composer を使用したテーマのインストール方法は、Composer を使用した他の拡張機能のインストール方法と同じです。 詳しくは、 [モジュールのインストール、管理、アップグレード](extensions.md) 」を参照してください。

Composer を使用してテーマをインストールするには：

1. テーマを「Commerce Marketplace」から購入します。
1. テーマのコンポーザー名を取得します。
1. Adobe Commerceのルートディレクトリに移動し、次のコマンドを入力します。

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
1. クリック **コンテンツ** /デザイン/ **テーマ**.

   テーマが右側のウィンドウに表示されます。

## 複数のテーマ

ロケールごとに異なるテーマなど、複数のテーマを使用する場合は、 `SCD_MATRIX` テーマの展開をカスタマイズするための環境変数。 詳しくは、 [ビルド](../environment/variables-build.md#scd_matrix) または [デプロイ](../environment/variables-deploy.md#scd_matrix) の段階 [環境設定](../environment/configure-env-yaml.md).
