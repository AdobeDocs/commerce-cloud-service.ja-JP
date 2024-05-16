---
title: システム固有の設定の管理例
description: クラウドインフラストラクチャ環境ですべてのAdobe Commerceでストア設定を管理および同期する方法の例を確認します。
hidefromtoc: true
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '865'
ht-degree: 0%

---


# システム固有の設定の管理例

この例では、構成管理を使用して、すべての環境でストア設定の一貫性を維持する方法を示します。

この例では、で定義された次のプロシージャを使用します。 [ストアの設定](store-settings.md):

1. 統合環境ストア管理者に設定を入力します。
1. を作成 `config.php` ファイルを作成し、ローカルワークステーションに転送します。
1. プッシュ `config.php` リモート統合環境に送信します。
1. 設定が管理者で編集できないことを確認します。
1. 必要な変更を加えます。

   * 統合環境の構成設定の変更
   * 設定を追加するには、コマンドを実行して以下を作成します `config.php` また。 新しい設定がファイルに追加されます。
   * 既存の設定を削除または編集するには、ファイルを手動で編集します。
   * コミットしてプッシュします。

例えば、次の設定が必要な場合があります。

* 無効 [locale](https://glossary.magento.com/locale) 統合環境におけるおよびの静的ファイル最適化設定
* ステージング環境および実稼動環境での静的ファイル最適化の有効化
* ステージング環境および実稼動環境での Fastly の設定に、それぞれの特定の資格情報を使用する

_静的ファイルの最適化_ は、JavaScript およびカスケードスタイルシートの結合と縮小、およびHTMLテンプレートの縮小を意味します。 参照： [静的コンテンツのデプロイメント戦略](../deploy/static-content.md).

## 前提条件

これらの設定管理タスクを完了するには、以下が必要です。

* を使用したプロジェクトリーダーの役割 [環境「admin」](../project/user-access.md) privileges
* 統合、ステージングおよび実稼動環境の管理者 URL と資格情報

## Commerce Admin の設定

統合環境では、管理者にログインして、ストア、Web サイト、モジュールまたは拡張機能のシステム設定、静的ファイル最適化、静的コンテンツのデプロイメントに関連するシステム値を変更できます。 参照： [設定データ](store-settings.md#scd-performance).

**ロケールと静的ファイルの最適化設定を変更するには**:

1. 統合環境管理者にログインします。 この URL には、 [[!DNL Cloud Console]](../project/overview.md).
1. に移動します。 **ストア** > 設定 > **設定** > 一般 > **一般**.
1. ページナビゲーションで、を展開します。 **ロケールオプション**.
1. から **Locale** リストでロケールを変更します。 後で元に戻すことができます。

   ![ロケールの変更](../../assets/locale-options.png)

1. クリック **設定を保存**.
1. プロンプトが表示された場合、 [キャッシュをフラッシュします](https://docs.magento.com/user-guide/system/cache-management.html).
1. 管理者からログアウトします。

## 値をエクスポートし、config.php をローカルシステムに転送します。

この手順では、を作成して転送します。 `config.php` ローカルマシンで実行するコマンドを使用した統合環境の設定ファイル。

この手順は、の手順 2 に対応します [推奨手順](store-settings.md). 作成後 `config.php`をローカルシステムに転送して、Git に追加できるようにします。

**作成して転送するには`config.php`**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. 統合環境に変更します。

   ```bash
   magento-cloud environment:checkout integration
   ```

1. リモート・データベースのローカル・ダンプを作成します。

   ```bash
   magento-cloud db:dump
   ```

次のスニペット： `config.php` デフォルトのロケールをに変更する例を示します `en_GB` 静的ファイル最適化設定の変更：

```php?start_inline=1
'general' => [
     'locale' => [
         'code' => 'en_GB',
         'timezone' => 'UTC',
     ],

     ... more ...

 'dev' => [
     'template' => [
         'allow_symlink' => '0',
         'minify_html' => '0',
     ],
     'js' => [
         'merge_files' => '0',
         'enable_js_bundling' => '0',
         'minify_files' => '0',
     ],
     'css' => [
         'merge_css_files' => '0',
         'minify_files' => '0',
     ],

     ... more ...
```

## config.php のプッシュと環境へのデプロイ

これで、を作成できました `config.php` ローカルシステムに転送し、Git にコミットして環境にプッシュします。 この手順は、の手順 3 および 4 に対応します [推奨手順](store-settings.md).

次のコマンドは、を追加、コミットし、にプッシュします `master` ブランチ：

```bash
git add app/etc/config.php && git commit -m "Add system-specific configuration" && git push origin master
```

ステージング環境および実稼動環境へのコードのデプロイメントを完了します。 スターターの場合、にプッシュします `staging` および `master` ブランチ。 展開コマンドの詳細については、を参照してください。 [ストアのデプロイ](../deploy/staging-production.md).

すべての環境でデプロイメントが完了するまで待ちます。

## 設定の変更を確認します

プッシュ後 `config.php` 環境に対して、変更した値は管理者では読み取り専用にする必要があります。 この例では、変更されたデフォルトのロケールと静的ファイル最適化設定は、管理者で編集できません。 これらの設定は、 `config.php`.

設定の変更を確認するには：

1. いずれかの環境で管理者からログアウトします。
1. 管理者に再度ログインします。
1. クリック **ストア** > 設定 > **設定** > 一般 > **一般**.
1. 右側のパネルで、を展開します **ロケールオプション**.

   次のサンプルに示すように、いくつかのフィールドは編集できないことに注意してください。 これらの設定は、で管理されます。 `config.php`.

   ![特定の値は管理者では編集できなくなりました](../../assets/locale-options-disabled.png)

1. 管理者からログアウトします。

## システム固有の構成設定の変更および更新

これらの設定を変更する必要がある場合は、 `config.php` テキストエディターを使用して手動でファイルします。 編集または削除を完了したら、前の手順に従ってコミットしてリモート環境にプッシュできます。

設定を追加するには、統合環境を変更し、コマンドを再度実行してファイルを生成します。 新しい設定はすべて、ファイルのコードに追加されます。 前の手順に従って、Git にプッシュします。

この例では、静的ファイル最適化設定を変更し、JavaScript の新しい設定を追加します。

### 統合での設定の追加

統合環境に設定値を追加するには、Admin. この例では、JavaScript ファイルを結合します。

1. 統合管理者からログアウトします。
1. 統合管理者に再度ログインします。
1. クリック **ストア** > 設定 > **設定** > **詳細** > **開発者**.
1. 右側のパネルで、を展開します **JavaScript 設定**.
1. から **JavaScript ファイルの結合** リスト、クリック **はい**.
1. クリック **設定を保存**.
1. プロンプトが表示された場合、 [キャッシュをフラッシュします](https://docs.magento.com/user-guide/system/cache-management.html).
1. 管理者からログアウトします。

dump コマンドを再度実行すると、新しい設定がファイルに追加されます。

```bash
magento-cloud db:dump
```

### 新しい設定で config.php を編集します。

ローカルで、テキストエディターを使用して更新されたを編集します `app/etc/config.php` ファイル。 これらの設定を編集して、JavaScript、HTML、CSS ファイルの縮小を有効にします。

```php?start_inline=1
 'dev' => [
     'template' => [
         'allow_symlink' => '0',
         'minify_html' => '0',
     ],

     ... more ...

     'js' => [
         'merge_files' => '0',
         'enable_js_bundling' => '0',
         'minify_files' => '0',
     ],
     'css' => [
         'merge_css_files' => '0',
         'minify_files' => '0',
     ],
```

縮小を許可するように設定を変更するには、次を編集します `'0'` 対象： `'1'` （用） `'minify_html'` およびそれぞれ `'minify_files'` オプション：

```php?start_inline=1
 'dev' => [
     'template' => [
         'allow_symlink' => '0',
         'minify_html' => '1',
     ],

     ... more ...

     'js' => [
         'merge_files' => '0',
         'enable_js_bundling' => '0',
         'minify_files' => '1',
     ],
     'css' => [
         'merge_css_files' => '0',
         'minify_files' => '1',
     ],
```

変更内容をファイルに保存します。

### 変更を Git にプッシュ

変更をプッシュするには、次を入力します。

```bash
git add app/etc/config.php
```

```bash
git commit -m "Add system-specific configuration and edit settings"
```

```bash
git push origin master
```

デプロイメントが完了するまで待ちます。

デプロイメントプロセスを繰り返して、コードをすべての環境にプッシュします。
