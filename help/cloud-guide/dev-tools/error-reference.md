---
title: ECE-Tools パッケージのエラーメッセージ
description: クラウドインフラストラクチャのビルド、デプロイ、デプロイ後のプロセスで、Adobe Commerce中に発生する可能性のあるエラーコードとメッセージのリストをご覧ください。
recommendations: noDisplay
role: Developer
exl-id: d8cc8d49-32da-43cf-a105-aa56b5334000
source-git-commit: 9dda6fe7f6a9d6064436820a3c8426ec982b5230
workflow-type: tm+mt
source-wordcount: '2763'
ht-degree: 4%

---

# ECE ツールのエラーメッセージ

このエラーメッセージリファレンスでは、クラウドインフラストラクチャー上のAdobe Commerceのビルド、デプロイおよびデプロイ後のプロセスで発生する可能性のあるエラーのトラブルシューティングについて説明します。

デプロイ中に発生する重大なエラーメッセージと警告エラーメッセージはすべて、両方の `var/log/cloud.log` および `/var/log/cloud.error.log` ファイル。 クラウドエラーログファイルには、最新のデプロイメントのエラーのみが含まれます。 空のファイルは、エラーのないデプロイメントが成功したことを示します。

が含まれる `cloud.error.log` ファイルの場合、各エントリは解析しやすいように、JSON 文字列としてフォーマットされます。

```json
{"errorCode":1006,"stage":"build","step":"validate-config","suggestion":"No stores/website/locales found in config.php\n  To speed up the deploy process do the following:\n  1. Using SSH, log in to your Magento Cloud account\n  2. Run \"php ./vendor/bin/ece-tools config:dump\"\n  3. Using SCP, copy the app/etc/config.php file to your local repository\n  4. Add, commit, and push your changes to the app/etc/config.php file","title":"The configured state is not ideal","type":"warning"}
```

エラーメッセージは、デプロイメントステージ（ビルド、デプロイ、デプロイ後）の 1 つで分類されます。 各セクションには、関連するエラーのリストと、各エラーに関する次の情報が表示されます。

- **エラーコード**:Adobe Commerceによって割り当てられたエラーメッセージの識別情報
- **ステージ**：エラーがビルド、デプロイ、またはデプロイ後のステージで発生したかどうかを示します
- **ステップ**：エラーを返すデプロイメントシナリオのステップを示します。 次の場合 _ステップ_ 列が空白の場合、エラーは一般的なエラーであり、複数の手順で返されたり、前処理の操作中に返されたりします。 参照： [シナリオベースのデプロイメント](../deploy/scenario-based.md) ビルド、デプロイ、デプロイ後の手順について詳しくは、こちらを参照してください。
- **提案**：エラーのトラブルシューティングと解決のガイダンスを提供します。
- **タイトル （エラーの説明）**：エラーの原因の概要を示す説明
- **タイプ**：エラーが重大エラーか警告かを示します

<!-- Note: The error code tables in this file are auto-generated from source code. To request changes to error code descriptions or suggestions, submit a GitHub issue to the magento/ece-tools repository. -->

## 重大なエラー

重大なエラーは、クラウドインフラストラクチャプロジェクト上のCommerce設定の問題によって、デプロイメントエラーが発生する問題（例えば、設定が正しくない、サポートされていない、必要な設定が見つからないなど）を示します。 デプロイする前に、設定を更新してこれらのエラーを解決する必要があります。

### ステージの作成

| エラーコード | ビルドステップ | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 2 |  | に書き込めません `./app/etc/env.php` ファイル | デプロイメントスクリプトで、に対して必要な変更を行うことはできません `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 3 |  | 設定がで定義されていません `schema.yaml` ファイル | 設定がで定義されていません `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 4 |  | を解析できませんでした `.magento.env.yaml` ファイル | この `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文を確認し、エラーを修正します。 |
| 5 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 6 |  | を読み取れません `.schema.yaml` ファイル | を読み取れません `./vendor/magento/ece-tools/config/magento.env.yaml` ファイル。 ファイルの権限を確認し、再デプロイします（`magento-cloud environment:redeploy`）に設定します。 |
| 7 | refresh-modules | に書き込めません `./app/etc/config.php` ファイル | 配置スクリプトは、に対して必要な変更を行うことができません `/app/etc/config.php` ファイル。 ファイルシステムの権限を確認します。 |
| 8 | validate-config | を読み取れません `composer.json` ファイル | を読み取れません `./composer.json` ファイル。 ファイルの権限を確認します。 |
| 9 | validate-config | この `composer.json` ファイルに必要な自動ロード セクションがありません | 必須 `autoload` セクションがにありません `composer.json` ファイル。 「自動読み込み」セクションと `composer.json` ファイルをクラウドテンプレートに追加し、不足している設定を追加します。 |
| 10 | validate-config | この `.magento.env.yaml` ファイルに、スキーマで宣言されていないオプションが含まれているか、無効な値またはステージで設定されたオプションが含まれています | この `./.magento.env.yaml` ファイルに無効な設定が含まれています。 詳細については、エラーログを確認してください。 |
| 11 | refresh-modules | コマンドが失敗しました： `/bin/magento module:enable --all` | 走ってみる `composer update` ローカル。 次に、更新されたをコミットしてプッシュします `composer.lock` ファイル。 また、 `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 12 | apply-patches | パッチを適用できませんでした |  |
| 13 | set-report-dir-nesting-level | ファイルに書き込めません `/pub/errors/local.xml` |  |
| 14 | copy-sample-data | サンプルデータファイルをコピーできませんでした |  |
| 15 | compile-di | コマンドが失敗しました： `/bin/magento setup:di:compile` | を確認します `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` 対象 `.magento.env.yaml` 詳細なコマンド出力については、を参照してください。 |
| 16 | dump-autoload | コマンドが失敗しました： `composer dump-autoload` | この `composer dump-autoload` コマンドが失敗しました。 を確認します `cloud.log` を参照してください。 |
| 17 | ランベーラー | 実行するコマンド `Baler` for Javascript のバンドルに失敗しました | を確認します `SCD_USE_BALER` 環境変数：Baler モジュールが設定され、JS バンドルが有効になっていることを検証します。 Baler モジュールが必要ない場合は、を設定します `SCD_USE_BALER: false`. |
| 18 | compress-static-content | 必要なユーティリティが見つかりませんでした（タイムアウト、bash） |  |
| 19 | deploy-static-content | コマンド `/bin/magento setup:static-content:deploy` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 20 | compress-static-content | 静的コンテンツの圧縮に失敗しました | を確認します `cloud.log` を参照してください。 |
| 21 | backup-data: static-content | 静的コンテンツをにコピーできませんでした `init` directory | を確認します `cloud.log` を参照してください。 |
| 22 | backup-data: writable-dirs | 一部の書き込み可能ディレクトリをにコピーできませんでした `init` directory | 書き込み可能ディレクトリをにコピーできませんでした `./init` フォルダー。 ファイルシステムの権限を確認します。 |
| 23 |  | ロガーオブジェクトを作成できません |  |
| 24 | backup-data: static-content | をクリーンアップできませんでした `./init/pub/static/` directory | クリーンアップできませんでした `./init/pub/static` フォルダー。 ファイルシステムの権限を確認します。 |
| 25 |  | Composer パッケージが見つかりません | Adobe Commerce アプリケーションのバージョンを GitHub リポジトリから直接インストールした場合は、次のことを確認してください `DEPLOYED_MAGENTO_VERSION_FROM_GIT` 環境変数が設定されます。 |
| 26 | validate-config | Adobe CommerceおよびMagento Open Source 2.4 以降でサポートされなくなったMagentoBraintreeモジュール設定を削除します。 | Braintreeモジュールのサポートは、Magento 2.4.0 以降には含まれなくなりました。 の「variables」セクションから CONFIG__STORES__DEFAULT__PAYMENT__VARIABLE__CHANNELBRAINTREEを削除します。 `.magento.app.yaml` ファイル。 Braintree支払いサポートについては、代わりにCommerce Marketplaceの公式の内線番号を使用してください。 |

### ステージのデプロイ

| エラーコード | デプロイステップ | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 101 | 事前デプロイ：キャッシュ | キャッシュの構成が正しくありません（ポートまたはホストがありません） | キャッシュ構成に必要なパラメーターがありません `server` または `port`. を確認します `cloud.log` を参照してください。 |
| 102 |  | に書き込めません `./app/etc/env.php` ファイル | デプロイメントスクリプトで、に対して必要な変更を行うことはできません `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 103 |  | 設定がで定義されていません `schema.yaml` ファイル | 設定がで定義されていません `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 104 |  | を解析できませんでした `.magento.env.yaml` ファイル | 設定がで定義されていません `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 105 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 106 |  | を読み取れません `.schema.yaml` ファイル |  |
| 107 | pre-deploy: clean-redis-cache | Redis キャッシュをクリーンアップできませんでした | Redis キャッシュをクリーンアップできませんでした。 Redis のキャッシュ構成が正しく、Redis サービスが利用可能であることを確認します。 参照： [Redis サービスのセットアップ](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/service/redis.html). |
| 108 | pre-deploy: set-production-mode | コマンド `/bin/magento maintenance:enable` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 109 | validate-config | データベース設定が正しくありません | を確認します。 `DATABASE_CONFIGURATION` 環境変数が正しく設定されている。 |
| 110 | validate-config | セッション設定が正しくありません | を確認します。 `SESSION_CONFIGURATION` 環境変数が正しく設定されている。 設定には、少なくとも次を含める必要があります `save` パラメーター。 |
| 111 | validate-config | 検索設定が正しくありません | を確認します。 `SEARCH_CONFIGURATION` 環境変数が正しく設定されている。 設定には、少なくとも次を含める必要があります `engine` パラメーター。 |
| 112 | validate-config | リソース設定が正しくありません | を確認します。 `RESOURCE_CONFIGURATION` 環境変数が正しく設定されている。 設定に含める必要のある最低文字種類の数 `connection` パラメーター。 |
| 113 | validate-config:elasticsuite-integrity | ElasticSuite がインストールされていますが、Elasticsearchサービスを利用できません | を確認します。 `SEARCH_CONFIGURATION` 環境変数が正しく設定されていることを確認し、Elasticsearchサービスが使用可能であることを確認します。 |
| 114 | validate-config:elasticsuite-integrity | ElasticSuite はインストールされていますが、別の検索エンジンが使用されています | ElasticSuite はインストールされていますが、別の検索エンジンが設定されています。 を更新 `SEARCH_CONFIGURATION` Elasticsearchを有効にし、Elasticsearchサービス設定を検証するための環境変数 `services.yaml` ファイル。 |
| 115 |  | データベースクエリの実行に失敗しました |  |
| 116 | install-update: setup | コマンド `/bin/magento setup:install` 失敗 | を確認します `cloud.log` および `install_upgrade.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 117 | install-update:config-import | コマンド `app:config:import` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 118 |  | 必要なユーティリティが見つかりませんでした（タイムアウト、bash） |  |
| 119 | install-update: deploy-static-content | コマンド `/bin/magento setup:static-content:deploy` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 120 | compress-static-content | 静的コンテンツの圧縮に失敗しました | を確認します `cloud.log` を参照してください。 |
| 121 | deploy-static-content:generate | デプロイされたバージョンを更新できません | を更新できません `./pub/static/deployed_version.txt` ファイル。 ファイルシステムの権限を確認します。 |
| 122 | clean-static-content | 静的コンテンツファイルをクリーンアップできませんでした |  |
| 123 | install-update:split-db | コマンド `/bin/magento setup:db-schema:split` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 124 | clean-view-preprocessed | をクリーンアップできませんでした `var/view_preprocessed` フォルダー | をクリーンアップできません `./var/view_preprocessed` フォルダー。 ファイルシステムの権限を確認します。 |
| 125 | install-update: reset-password | を更新できませんでした `/var/credentials_email.txt` ファイル | を更新できませんでした `/var/credentials_email.txt` ファイル。 ファイルシステムの権限を確認します。 |
| 126 | install-update:update | コマンド `/bin/magento setup:upgrade` 失敗 | を確認します `cloud.log` および `install_upgrade.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 127 | キャッシュのクリーンアップ | コマンド `/bin/magento cache:flush` 失敗 | を確認します `cloud.log` を参照してください。 コマンド出力について詳しくは、 `VERBOSE_COMMANDS: '-vvv'` のオプション `.magento.env.yaml` ファイル。 |
| 128 | disable-maintenance-mode | コマンド `/bin/magento maintenance:disable` 失敗 | を確認します `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` 対象 `.magento.env.yaml` 詳細なコマンド出力については、を参照してください。 |
| 129 | install-update: reset-password | パスワードリセットテンプレートを読み取れません |  |
| 130 | install-update:cache_type | コマンドが失敗しました： `php ./bin/magento cache:enable` | コマンド `php ./bin/magento cache:enable` Adobe Commerceがインストールされているが `./app/etc/env.php` デプロイメントの開始時に、ファイルが存在しないか空でした。 を確認します `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` 対象 `.magento.env.yaml` 詳細なコマンド出力については、を参照してください。 |
| 131 | install-update | この `crypt/key`  キー値がに存在しません `./app/etc/env.php` ファイルまたは `CRYPT_KEY` クラウド環境変数 | このエラーは、 `./app/etc/env.php` Adobe Commerceのデプロイメント開始時、または `crypt/key` 値が定義されていません。 別の環境からデータベースを移行した場合は、その環境から暗号化キーの値を取得します。 次に、値をに追加します [CRYPT_KEY](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/env/stage/variables-deploy.html#crypt_key) 現在の環境のクラウド環境変数。 参照： [Adobe Commerce暗号化キー](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/develop/overview.html#gather-credentials). 誤って削除した場合 `./app/etc/env.php` ファイル。次のコマンドを使用して、以前の配置で作成されたバックアップ ファイルから復元します。 `./vendor/bin/ece-tools backup:restore` CLI コマンド。&quot; |
| 132 |  | Elasticsearchサービスに接続できません | 有効なElasticsearch資格情報を確認し、サービスが実行中であることを確認します |
| 137 |  | OpenSearch サービスに接続できません | 有効な OpenSearch 認証情報を確認し、サービスが実行中であることを確認します |
| 133 | validate-config | Adobe CommerceまたはMagento Open Source 2.4 以降でサポートされなくなったMagentoBraintreeモジュール設定を削除します。 | Braintreeモジュールのサポートは、Adobe CommerceまたはMagento Open Source 2.4.0 以降には含まれなくなりました。 の「variables」セクションから CONFIG__STORES__DEFAULT__PAYMENT__VARIABLE__CHANNELBRAINTREEを削除します。 `.magento.app.yaml` ファイル。 Braintreeのサポートについては、Commerce Marketplaceの公式のBraintree支払い拡張機能を使用します。 |
| 134 | validate-config | Adobe CommerceおよびMagento Open Source 2.4.0 では、Elasticsearchサービスをインストールする必要があります | Elasticsearchサービスのインストール |
| 138 | validate-config | Adobe CommerceおよびMagento Open Source 2.4.4 では、OpenSearch またはElasticsearchサービスをインストールする必要があります | OpenSearch サービスのインストール |
| 135 | validate-config | 検索エンジンは、Adobe CommerceのElasticsearchおよびMagento Open Source >= 2.4.0 に設定する必要があります | の SEARCH_CONFIGURATION 変数を確認します。 `engine` オプション。 設定されている場合は、オプションを削除するか、値を「elasticsearch」に設定します。 |
| 136 | validate-config | Split Database は、Adobe CommerceおよびMagento Open Source 2.5.0 以降で削除されました。 | 分割データベースを使用している場合は、単一のデータベースに戻すか、単一のデータベースに移行するか、別の方法を使用する必要があります。 |
| 139 | validate-config | 検索エンジンが正しくありません | このAdobe CommerceまたはMagento Open Sourceのバージョンでは、OpenSearch がサポートされていません。 バージョン 2.3.7-p3、2.4.3-p2 以降を使用する必要があります |

### デプロイ後のステージ

| エラーコード | デプロイ後手順 | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 201 | is-deploy-failed | ステージのデプロイに失敗しました |  |
| 202 |  | この `./app/etc/env.php` ファイルは書き込み可能ではありません | デプロイメントスクリプトで、に対して必要な変更を行うことはできません `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 203 |  | 設定がで定義されていません `schema.yaml` ファイル | 設定がで定義されていません `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 204 |  | を解析できませんでした `.magento.env.yaml` ファイル | この `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文を確認し、エラーを修正します。 |
| 205 |  | を読み取れません `.magento.env.yaml` ファイル | ファイルの権限を確認します。 |
| 206 |  | を読み取れません `.schema.yaml` ファイル |  |
| 207 | ウォームアップ | いくつかのウォームアップページをプリロードできませんでした |  |
| 208 | 最初のバイトまでの時間 | 最初のバイト （TTFB）への時間のテストに失敗しました |  |
| 227 | キャッシュのクリーンアップ | コマンド `/bin/magento cache:flush` 失敗 | を確認します `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` 対象 `.magento.env.yaml` 詳細なコマンド出力については、を参照してください。 |

### 一般

| エラーコード | 一般的な手順 | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 243 |  | 設定がで定義されていません `schema.yaml` ファイル | 設定変数名が正しく、定義されていることを確認します。 |
| 244 |  | を解析できませんでした `.magento.env.yaml` ファイル | この `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文を確認し、エラーを修正します。 |
| 245 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 246 |  | を読み取れません `.schema.yaml` ファイル |  |
| 247 |  | イベント用のモジュールを生成できません | を確認します `cloud.log` を参照してください。 |
| 248 |  | イベント用のモジュールを有効にできません | を確認します `cloud.log` を参照してください。 |
| 249 |  | AdobeCommerceWebhookPlugins モジュールを生成できませんでした | を確認します `cloud.log` を参照してください。 |
| 250 |  | AdobeCommerceWebhookPlugins モジュールを有効にできませんでした | を確認します `cloud.log` を参照してください。 |

## 警告エラー

警告エラーは、クラウドインフラストラクチャプロジェクト上のCommerceの設定に関する問題（誤った、非推奨、サポートされていない、オプション機能の設定が見つからないなど、サイトの操作に影響を与える可能性がある）を示します。 警告によって配置の失敗が発生することはありませんが、警告メッセージを確認し、それらを解決するために構成を更新する必要があります。

### ステージの作成

| エラーコード | ビルドステップ | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 1001 | validate-config | ファイル app/etc/config.phpは存在しません |  |
| 1002 | validate-config | を使用します。/build_options.ini ファイルはサポートされなくなりました |  |
| 1003 | validate-config | 共有構成ファイルにモジュール セクションがありません |  |
| 1004 | validate-config | この設定は、このバージョンのMagentoと互換性がありません |  |
| 1005 | validate-config | 無視された SCD オプション |  |
| 1006 | validate-config | 設定された状態は理想的ではありません |  |
| 1007 | ランベーラー | Baler JS のバンドルは使用できません |  |

### ステージのデプロイ

| エラーコード | デプロイステップ | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 2001 | pre-deploy:cache | キャッシュは、使用できない Redis サービス用に設定されています。 設定は無視されます。 |  |
| 2002 | validate-config | 設定された状態は理想的ではありません |  |
| 2003 | validate-config | エラーレポート用のディレクトリのネスティングレベル値が設定されていません |  |
| 2004 | validate-config | での設定が無効です。/pub/errors/local.xml ファイル。 |  |
| 2005 | validate-config | 管理データは、初期インストール時にのみ管理者ユーザーを作成するために使用されます。 アップグレードプロセス中、管理者データに対する変更は無視されます。 | 最初のインストール後、管理者データを設定から削除できます。 |
| 2006 | validate-config | 管理者の電子メールが設定されていなかったので、管理者ユーザーは作成されませんでした | インストール後、管理者ユーザーを手動で作成できます。ssh を使用して環境に接続します。 次に、 `bin/magento admin:user:create` コマンド。 |
| 2007 | validate-config | php のバージョンを推奨バージョンに更新する |  |
| 2008 | validate-config | Solr のサポートは、Adobe CommerceおよびMagento Open Source 2.1 で非推奨（廃止予定）となりました。 |  |
| 2009 | validate-config | Solr は、Adobe CommerceおよびMagento Open Source 2.2 以降ではサポートされなくなりました。 |  |
| 2010 | validate-config | Elasticsearchサービスは、インフラストラクチャレイヤーにインストールされますが、検索エンジンとしては使用されません。 | リソースの使用を最適化するには、インフラストラクチャレイヤーからElasticsearchサービスを削除することを検討してください。 |
| 2011 | validate-config | インフラストラクチャレイヤーのElasticsearchサービスバージョンは、Adobe Commerce アプリケーションで使用されている現在のバージョンの elasticsearch/elasticsearch モジュールと互換性がありません。 |  |
| 2012 | validate-config | 現在の設定は、このバージョンのAdobe Commerceと互換性がありません |  |
| 2013 | validate-config | デプロイプロセスがビルドフェーズで実行されなかったので、SCD オプションは無視されました |  |
| 2014 | validate-config | 設定に、非推奨の変数または値が含まれています |  |
| 2015 | validate-config | 環境設定が無効です |  |
| 2016 | validate-config | JSON タイプの設定をデコードできません |  |
| 2017 | validate-config | 現在の設定は、このバージョンのAdobe Commerceと互換性がありません |  |
| 2018 | validate-config | 一部のサービスは提供終了（EOL）になりました |  |
| 2019 | validate-config | MySQL 検索設定オプションは非推奨です | 代わりに、Elasticsearchを使用します。 |
| 2029 | validate-config | Split Database は、Adobe CommerceおよびMagento Open Source 2.4.2 で非推奨となり、2.5 で削除される予定です。 | 分割データベースを使用している場合は、単一のデータベースに戻す、単一のデータベースに移行する、または別の方法を使用する計画を開始する必要があります。 |
| 2020 | install-update | Adobe Commerceのインストールは完了しましたが、 `app/etc/env.php` 構成ファイルが見つからないか、空です。 | 必須データは、環境設定および.magento.env.yaml ファイルから復元されます。 |
| 2021 | install-update:db-connection | カスタム接続を使用する分割データベースの場合 |  |
| 2022 | install-update:db-connection | スレーブ接続と互換性のないデータベース構成に変更しました。 |  |
| 2023 | install-update:split-db | 分割データベースの有効化はスキップされます。 |  |
| 2024 | install-update:split-db | SPLIT_DB 変数に分割接続タイプの構成がありません。 |  |
| 2025 | install-update:split-db | スレーブ接続が設定されていません。 |  |
| 2026 | pre-deploy:restore-writable-dirs | ビルド フェーズで生成された一部のデータをマウントされたディレクトリに復元できませんでした | を確認します `cloud.log` を参照してください。 |
| 2027 | validate-config:mage-mode-variable | MAGE_MODE 環境変数のモード値はサポートされていません | MAGE_MODE 環境変数を削除するか、その値を「production」に変更します。 クラウドインフラストラクチャー上のAdobe Commerceでは、「実稼動」モードのみをサポートします。 |
| 2028 | remote-storage | リモート記憶域を有効にできませんでした。 | リモートストレージの資格情報を確認します。 |
| 2030 | validate-config | Elasticsearchサービスと OpenSearch サービスは、どちらもインフラストラクチャレイヤーにインストールされます。 Adobe CommerceおよびMagento Open Source 2.4.4 以降では、デフォルトで OpenSearch を使用します | リソースの使用状況を最適化するには、インフラストラクチャレイヤーからElasticsearchサービスまたは OpenSearch サービスを削除することを検討してください。 |

### デプロイ後のステージ

| エラーコード | デプロイ後手順 | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 3001 | validate-config | Adobe Commerceではデバッグログが有効になっています | ディスク容量を節約するには、実稼動環境のデバッグログを有効にしないでください。 |
| 3002 | ウォームアップ | ストア URL を取得できません |  |
| 3003 | ウォームアップ | ストア URL を取得できません |  |
| 3004 | バックアップ | バックアップ ファイルを作成できません |  |

### 一般

| エラーコード | 一般的な手順 | エラーの説明（タイトル） | 推奨されるアクション |
| - | - | - | - |
| 4001 |  | システム プロセッサ数を取得できません： |  |
