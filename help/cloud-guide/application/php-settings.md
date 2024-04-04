---
title: PHP 設定
description: クラウドインフラストラクチャでのコマースアプリケーション設定の最適な PHP 設定について説明します。
feature: Cloud, Configuration, Extensions
exl-id: b4180265-f7a1-48e4-8c23-27835253e171
source-git-commit: 9b3772cf640ebc56063434e1aa8acb1ec51dc63c
workflow-type: tm+mt
source-wordcount: '553'
ht-degree: 0%

---

# PHP 設定

どちらを選択できますか [PHP のバージョン](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) 走って `.magento.app.yaml` ファイル：

```yaml
name: mymagento
type: php:<version>
```

>[!TIP]
>
>PHP 8.1 以降にアップグレードする場合は、 [`runtime: extensions:` プロパティ](properties.md#runtime) （内） `.magento.app.yaml` ファイルを作成し、再デプロイします。 JSON 拡張機能は、PHP 8.0 以降、クラウド環境にインストールされます。

## PHP の設定

PHP の設定をカスタマイズするには、 `php.ini` Adobe Commerceで管理される設定に追加されるファイル。

リポジトリで、 `php.ini` ファイルをアプリケーションのルート（リポジトリのルート）にコピーします。

>[!TIP]
>
>PHP の設定が不適切な場合、問題が発生する可能性があるので、上級の管理者だけがこれらのオプションを設定する必要があります。

### PHP のメモリ制限の引き上げ

PHP のメモリ制限を増やすには、次の設定を `php.ini` ファイル：

```ini
memory_limit = 1G
```

デバッグの場合は、の値を 2G に増やします。

### realpath_cache の設定を最適化

次の設定を行います。 `realpath_cache` アプリケーションのパフォーマンスを向上させるための設定。

```conf
;
; Increase realpath cache size
;
realpath_cache_size = 10M

;
; Increase realpath cache ttl
;
realpath_cache_ttl = 7200
```

これらの設定を使用すると、PHP プロセスは、ページの読み込みごとにファイルを検索する代わりに、ファイルへのパスをキャッシュできます。 詳しくは、 [パフォーマンスの調整](https://www.php.net/manual/en/ini.core.php) PHP ドキュメント内。

>[!NOTE]
>
>推奨される PHP の設定の一覧については、 [必要な PHP 設定](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/php-settings.html) （内） _インストールガイド_.

### カスタム PHP 設定を確認する

をプッシュした後 `php.ini` クラウド環境を変更した場合は、カスタム PHP 設定が環境に追加されていることを確認できます。 例えば、SSH を使用してリモート環境にログインし、次のような方法でファイルを表示します。

```bash
cat /etc/php/<php-version>/fpm/php.ini
```

>[!WARNING]
>
>Cloud Docker for Commerce をローカル開発に使用する場合は、 [Docker サービスコンテナ](https://developer.adobe.com/commerce/cloud-tools/docker/containers/service/#fpm-container) カスタム `php.ini` ファイルを Docker 環境で作成する必要があります。

## 拡張機能の有効化

PHP 拡張機能は、 `runtime:extension` 」セクションに入力します。 また、指定された拡張機能は、Docker PHP コンテナで使用できるようになります。

>[!IMPORTANT]
>
>拡張を有効にする前に、PHP のバージョンが、プロジェクトをホストするオペレーティングシステムと互換性がある必要があることを理解しておくことが重要です。 プロジェクト環境を進める前に、インフラストラクチャチームによる OS のアップグレードが必要になる場合があります。

の例 `.magento.app.yaml` ファイル：

```yaml
runtime:
    extensions:
        - sockets
        - sodium
        - ssh2
    disabled_extensions:
        - bcmath
        - bz2
        - calendar
        - exif
```

SSH を使用して環境にログインし、PHP 拡張をリストします。

```bash
php -m
```

特定の PHP 拡張機能について詳しくは、 [PHP 拡張リスト](https://www.php.net/manual/en/extensions.alphabetical.php).

次の表に、クラウドプラットフォームにAdobe Commerceをデプロイする際にサポートされる PHP 拡張を示します。

| デフォルトの拡張機能 | インストール済みの拡張機能<br>アンインストールできない | インストール可能な拡張機能<br>必要に応じてアンインストール |
| ------------------ | --------------------- | --------------------- |
| `bcmath`<br>`bz2`<br>`calendar`<br>`exif`<br>`gd`<br>`gettext`<br> `intl`<br> `mysqli`<br> `openswoole`<br> `pcntl`<br> `pdo_mysql`<br> `soap`<br> `sockets`<br>  `sysvmsg`<br> `sysvsem`<br> `sysvshm`<br> `opcache`<br> `zip` | `ctype`<br> `curl`<br>`date`<br> `dom`<br> `fileinfo`<br> `filter`<br> `ftp`<br> `hash`<br> `iconv`<br> `json`<br> `mbstring`<br> `mysqlnd`<br> `openssl`<br> `pcre`<br> `pdo`<br> `pdo_sqlite`<br> `phar`<br>`posix`<br> `readline`<br> `session`<br> `sqlite3`<br> `tokenizer`<br> `xml`<br> `xmlreader`<br> `xmlwriter`<br> | `geoip`<br>`gmp`<br> `igbinary`<br> `imagick`<br> `imap`<br> `ioncube` <br>`ldap`<br> `mailparse`<br> `mcrypt`<br> `msgpack`<br> `mysqli`<br> `oauth`<br> `pdo_mysql`<br> `propro`<br> `pspell`<br> `raphf`<br> `recode`<br> `redis`<br> `shmop` `sockets`<br> `sodium`<br> `ssh2`<br>`tidy`<br> `xdebug`<br> `xmlrpc`<br> `xsl`<br> `yaml` |

PHP モジュールの要件は、Adobe Commerceのバージョンに結び付けられています。 詳しくは、 [PHP の要件](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/php-settings.html).

### 拡張機能のサポート

Pro プロジェクトの場合、次の拡張機能をインストールするには、追加のサポートが必要です。

- `sourceguardian`

例えば、すべての環境で SourceGuardian-protected スクリプトのみを実行するように PHP を設定するには、次のオプションを `php.ini` ファイル：

```ini
[SourceGuardian]
sourceguardian.restrict_unencoded = "1"
```

詳しくは、 [SourceGuardian ドキュメントの 3.5 節](https://sourceguardian.com/demofiles/files/SourceGuardian%20for%20Linux%20User%20Manual.pdf). _これはPDFへのリンクです_.

[Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) を参照してください。 更新した `.magento/services.yaml` ファイル `.magento.app.yaml` ファイルに、更新された PHP バージョンと追加の PHP 拡張子が含まれます。 実稼動環境に対する変更の通知は、48 時間以上にする必要があります。 Cloud インフラストラクチャチームがプロジェクトを更新するまでに最大 48 時間かかる場合があります。

>[!WARNING]
>
>debug でコンパイルされた PHP はサポートされておらず、Probe が [!DNL XDebug] または [!DNL XHProf]. プローブを有効にする際に、これらの拡張を無効にします。 Probe は、 [!DNL Pinba] または IonCube。
