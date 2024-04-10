---
title: PHP 設定
description: クラウドインフラストラクチャにおけるコマースアプリケーション設定に最適な PHP 設定について説明します。
feature: Cloud, Configuration, Extensions
exl-id: b4180265-f7a1-48e4-8c23-27835253e171
source-git-commit: 94c1e16a07567471d446478e3bd2a33977247ef3
workflow-type: tm+mt
source-wordcount: '536'
ht-degree: 0%

---

# PHP 設定

以下から選択できます [php のバージョン](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/system-requirements.html) を実行するには `.magento.app.yaml` ファイル：

```yaml
name: mymagento
type: php:<version>
```

>[!TIP]
>
>PHP 8.1 以降にアップグレードする場合は、 [`runtime: extensions:` プロパティ](properties.md#runtime) が含まれる `.magento.app.yaml` ファイルを作成し、再デプロイします。 JSON 拡張機能は、PHP 8.0 以降、クラウド環境にインストールされています。

## PHP の設定

を使用して、ご使用の環境に合わせて PHP 設定をカスタマイズできます。 `php.ini` Adobe Commerceが管理する設定に追加されるファイル。

リポジトリに、次を追加します `php.ini` ファイルをアプリケーションのルート（リポジトリルート）に移動します。

>[!TIP]
>
>PHP の設定を適切に行わないと問題が発生する可能性があるため、これらのオプションを設定するのは上級管理者のみにしてください。

### PHP のメモリ制限を増やす

PHP のメモリ制限を増やすには、以下の設定を `php.ini` ファイル：

```ini
memory_limit = 1G
```

デバッグするには、値を 2G に増やします。

### realpath_cache 構成の最適化

以下を設定します `realpath_cache` アプリケーションのパフォーマンスを向上させるための設定。

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

これらの設定により、PHP プロセスは、ページが読み込まれるたびにパスを検索する代わりにファイルへのパスをキャッシュすることができます。 参照： [パフォーマンスチューニング](https://www.php.net/manual/en/ini.core.php) PHP のドキュメントに書かれています。

>[!NOTE]
>
>推奨される PHP の設定の一覧については、を参照してください。 [必要な PHP 設定](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/php-settings.html) が含まれる _インストールガイド_.

### カスタム PHP 設定の確認

をプッシュした後 `php.ini` クラウド環境の変更について、カスタム PHP 設定が環境に追加されたことを確認します。 例えば、SSH を使用してリモート環境にログインし、次のような方法でファイルを表示します。

```bash
cat /etc/php/<php-version>/fpm/php.ini
```

>[!WARNING]
>
>ローカル開発に Cloud Docker for Commerce を使用する場合は、以下を参照してください。 [Docker サービスコンテナ](https://developer.adobe.com/commerce/cloud-tools/docker/containers/service/#fpm-container) カスタムの使用について `php.ini` docker 環境のファイル。

## 拡張機能の有効化

で PHP 拡張機能を有効または無効にできます。 `runtime:extension` セクション。 また、指定された拡張機能は、Docker PHP コンテナで使用できるようになります。

>[!IMPORTANT]
>
>拡張機能を有効にする前に、PHP バージョンがプロジェクトをホストするオペレーティングシステムと互換性を持つ必要があることを理解しておくことが重要です。 プロジェクト環境では、続行する前に、インフラストラクチャチームによる OS のアップグレードが必要になる場合があります。

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

SSH を使用して環境にログインし、PHP の拡張機能を一覧表示します。

```bash
php -m
```

特定の PHP 拡張モジュールの詳細については、 [PHP 拡張機能リスト](https://www.php.net/manual/en/extensions.alphabetical.php).

次の表に、Cloud Platform にAdobe Commerceをデプロイする際にサポートされる PHP 拡張機能を示します。

{{$include /help/_includes/templated/php-extensions-cloud.md}}

PHP のモジュール要件は、Adobe Commerceのバージョンに関連付けられています。 参照： [PHP 要件](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/prerequisites/php-settings.html).

### 拡張機能のサポート

Pro プロジェクトの場合、次の拡張機能のインストールには追加のサポートが必要です。

- `sourceguardian`

例えば、すべての環境で SourceGuardian 保護スクリプトのみを実行するように PHP を設定するには、以下のオプションを `php.ini` ファイル：

```ini
[SourceGuardian]
sourceguardian.restrict_unencoded = "1"
```

参照： [sourceguardian ドキュメントのセクション 3.5](https://sourceguardian.com/demofiles/files/SourceGuardian%20for%20Linux%20User%20Manual.pdf). _PDFへのリンクです_.

[Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) これらの PHP 拡張機能をすべての実稼動環境および Pro ステージング環境にインストールする際のヘルプ 更新済みを含める `.magento/services.yaml` ファイル、 `.magento.app.yaml` 更新された PHP のバージョンと、その他の PHP の拡張子を持つファイル。 実稼動環境に変更を加える場合は、少なくとも 48 時間は通知する必要があります。 クラウドインフラストラクチャチームがプロジェクトを更新するまで、最大 48 時間かかる場合があります。

>[!WARNING]
>
>debug を指定してコンパイルされた PHP はサポートされておらず、Probe は以下と競合する可能性があります [!DNL XDebug] または [!DNL XHProf]. プローブを有効にする場合は、これらの拡張機能を無効にします。 Probe は以下のような PHP 拡張モジュールと競合します。 [!DNL Pinba] または IonCube。
