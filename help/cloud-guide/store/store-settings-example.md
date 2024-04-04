---
title: システム固有の設定の管理例
description: クラウドインフラストラクチャ環境上のすべてのAdobe Commerceでストア設定を管理および同期する方法の例を参照してください。
hidefromtoc: true
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '865'
ht-degree: 0%

---


# システム固有の設定の管理例

この例では、設定管理を使用して、すべての環境でストア設定の一貫性を維持する方法を示します。

この例では、 [ストア設定](store-settings.md):

1. 統合環境のストア管理者で設定を入力します。
1. の作成 `config.php` ファイルを作成し、ローカルワークステーションに転送します。
1. プッシュ `config.php` をリモート統合環境に追加します。
1. 管理者で設定が編集できないことを確認します。
1. 必要に応じて次の変更を行います。

   * 統合環境の設定を変更します。
   * 設定を追加するには、コマンドを実行して `config.php` 再び 新しい設定がファイルに追加されます。
   * 既存の設定を削除または編集するには、ファイルを手動で編集します。
   * コミットしてプッシュします。

例えば、次の設定を行うことができます。

* 無効にする [ロケール](https://glossary.magento.com/locale) および統合環境での静的ファイル最適化設定
* ステージング環境と実稼動環境で静的ファイル最適化を有効にする
* 各に固有の資格情報を使用して、ステージングおよび実稼動環境で Fastly を設定する

_静的ファイルの最適化_ とは、JavaScript とカスケーディングスタイルシートの結合と縮小、およびHTMLテンプレートの縮小を意味します。 詳しくは、 [静的コンテンツデプロイメント戦略](../deploy/static-content.md).

## 前提条件

これらの設定管理タスクを完了するには、次の手順を実行する必要があります。

* とのプロジェクトリーダーの役割 [environment &quot;admin&quot;](../project/user-access.md) 権限
* 統合、ステージングおよび実稼動環境の管理 URL と資格情報

## コマース管理者の設定

統合環境では、管理者にログインして、ストア、Web サイト、モジュールまたは拡張機能のシステム設定、静的ファイルの最適化、静的コンテンツのデプロイメントに関連するシステム値を変更できます。 詳しくは、 [設定データ](store-settings.md#scd-performance).

**ロケールと静的ファイルの最適化設定を変更するには**:

1. 統合環境の管理者にログインします。 この URL には、 [[!DNL Cloud Console]](../project/overview.md).
1. に移動します。 **ストア** /設定/ **設定** /一般/ **一般**.
1. ページナビゲーションで、を展開します。 **ロケールオプション**.
1. 次から： **ロケール** リストで、ロケールを変更します。 後で元に戻すことができます。

   ![ロケールの変更](../../assets/locale-options.png)

1. クリック **設定を保存**.
1. プロンプトが表示された場合、 [キャッシュをフラッシュ](https://docs.magento.com/user-guide/system/cache-management.html).
1. 管理者からログアウトします。

## 値を書き出し、config.php をローカルシステムに転送します。

この手順では、 `config.php` ローカルマシン上で実行するコマンドを使用して、統合環境上の設定ファイルを作成します。

この手順は、 [推奨手順](store-settings.md). 作成後 `config.php`を設定し、ローカルシステムに転送して、Git に追加できるようにします。

**を作成して転送するには`config.php`**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. 統合環境に変更します。

   ```bash
   magento-cloud environment:checkout integration
   ```

1. リモート・データベースのローカル・ダンプを作成します。

   ```bash
   magento-cloud db:dump
   ```

次のスニペット： `config.php` デフォルトのロケールを `en_GB` 静的ファイル最適化設定の変更：

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

## config.php を環境にプッシュしてデプロイします。

これで、 `config.php` をローカルシステムに転送し、それを Git にコミットして、環境にプッシュします。 この手順は、 [推奨手順](store-settings.md).

次のコマンドは、 `master` ブランチ：

```bash
git add app/etc/config.php && git commit -m "Add system-specific configuration" && git push origin master
```

ステージング環境および実稼動環境へのコードのデプロイメントを完了します。 スターターの場合は、次の場所にプッシュします。 `staging` および `master` 分岐。 配置コマンドの詳細については、 [ストアをデプロイ](../deploy/staging-production.md).

すべての環境でデプロイメントが完了するのを待ちます。

## 設定の変更を確認します。

プッシュ後 `config.php` を環境に追加する場合、変更した値は管理者で読み取り専用にする必要があります。 この例では、変更されたデフォルトロケールと静的ファイル最適化設定を管理で編集できないようにする必要があります。 これらの設定は、 `config.php`.

設定の変更を検証するには、次の手順に従います。

1. いずれかの環境で管理者からログアウトします。
1. 管理者に再度ログインします。
1. クリック **ストア** /設定/ **設定** /一般/ **一般**.
1. 右側のウィンドウで、を展開します。 **ロケールオプション**.

   次の例に示すように、いくつかのフィールドは編集できません。 これらの設定は、 `config.php`.

   ![管理で編集できなくなった特定の値](../../assets/locale-options-disabled.png)

1. 管理者からログアウトします。

## システム固有の構成設定の変更と更新

これらの設定のいずれかを変更する必要がある場合は、 `config.php` ファイルを手動で作成します。 編集または削除が完了したら、前の手順に従って、コミットし、リモート環境にプッシュできます。

設定を追加するには、統合環境を変更し、コマンドを再実行してファイルを生成します。 新しい設定は、ファイル内のコードに追加されます。 前の手順に従って、Git にプッシュします。

この例では、静的ファイル最適化設定を変更し、JavaScript 用の新しい設定を追加します。

### 統合での設定の追加

統合環境の管理者で設定値を追加するには、次の手順を実行します。 次の例では、JavaScript ファイルを結合します。

1. 統合管理者からログアウトします。
1. 統合管理者に再度ログインします。
1. クリック **ストア** /設定/ **設定** > **詳細** > **開発者**.
1. 右側のウィンドウで、を展開します。 **JavaScript 設定**.
1. 次から： **JavaScript ファイルの結合** リスト、クリック **はい**.
1. クリック **設定を保存**.
1. プロンプトが表示された場合、 [キャッシュをフラッシュ](https://docs.magento.com/user-guide/system/cache-management.html).
1. 管理者からログアウトします。

ダンプコマンドを再度実行すると、新しい設定がファイルに追加されます。

```bash
magento-cloud db:dump
```

### 新しい設定で config.php を編集

ローカルで、テキストエディターを使用して、更新した `app/etc/config.php` ファイル。 これらの設定を編集して、JavaScript、HTML、CSS ファイル用の縮小を有効にします。

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

縮小化を許可する設定を変更するには、次を編集します。 `'0'` から `'1'` 対象： `'minify_html'` および `'minify_files'` オプション：

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

### 変更を Git にプッシュします

変更をプッシュするには、以下を入力します。

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

すべての環境にコードをプッシュするために、デプロイメントプロセスを繰り返します。
