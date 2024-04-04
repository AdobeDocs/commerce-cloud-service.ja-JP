---
title: ECE-Tools パッケージのエラーメッセージ
description: クラウドインフラストラクチャのビルド、デプロイ、デプロイ後のプロセスで、Adobe Commerceの実行中に発生する可能性のあるエラーコードおよびメッセージの一覧を確認します。
recommendations: noDisplay
role: Developer
exl-id: d8cc8d49-32da-43cf-a105-aa56b5334000
source-git-commit: f8e35ecff4bcafda874a87642348e2d2bff5247b
workflow-type: tm+mt
source-wordcount: '2719'
ht-degree: 4%

---

# ECE-Tools のエラーメッセージ

このエラーメッセージリファレンスは、クラウドインフラストラクチャのビルド、デプロイ、デプロイ後のプロセスでAdobe Commerceの実行中に発生する可能性のあるエラーのトラブルシューティングに関する情報を提供します。

デプロイ時に発生するすべての重大なエラーメッセージと警告エラーメッセージは、 `var/log/cloud.log` および `/var/log/cloud.error.log` ファイル。 クラウドエラーログファイルには、最新のデプロイメントからのエラーのみが含まれています。 空のファイルは、エラーなしでデプロイメントが成功したことを示します。

Adobe Analytics の `cloud.error.log` ファイルに指定する場合、各エントリは、解析しやすいように JSON 文字列形式になります。

```json
{"errorCode":1006,"stage":"build","step":"validate-config","suggestion":"No stores/website/locales found in config.php\n  To speed up the deploy process do the following:\n  1. Using SSH, log in to your Magento Cloud account\n  2. Run \"php ./vendor/bin/ece-tools config:dump\"\n  3. Using SCP, copy the app/etc/config.php file to your local repository\n  4. Add, commit, and push your changes to the app/etc/config.php file","title":"The configured state is not ideal","type":"warning"}
```

エラーメッセージは、ビルド、デプロイ、デプロイ後のデプロイメントステージの 1 つ別に分類されます。 各セクションには、関連するエラーのリストと各エラーに関する次の情報が表示されます。

- **エラーコード**:Adobe Commerceがエラーメッセージに割り当てた識別子
- **ステージ**：ビルド、デプロイ、デプロイ後のステージでエラーが発生したかどうかを示します
- **手順**：エラーを返す可能性のあるデプロイメントシナリオの手順を示します。 次の場合、 _手順_ 列が空白の場合、エラーは、複数の手順で返すことができる一般的なエラー、または前処理操作時に返されるエラーです。 詳しくは、 [シナリオベースのデプロイメント](../deploy/scenario-based.md) を参照してください。
- **修正候補**：エラーのトラブルシューティングと解決に関するガイダンスを提供します
- **タイトル（エラーの説明）**：エラーの原因を要約した説明
- **タイプ**：エラーが重大なエラーか警告かを示します

<!-- Note: The error code tables in this file are auto-generated from source code. To request changes to error code descriptions or suggestions, submit a GitHub issue to the magento/ece-tools repository. -->

## 重大なエラー

重大なエラーは、クラウドインフラストラクチャ上のAdobe Commerceのプロジェクト設定に問題があることを示しています。この問題により、デプロイメントに失敗する原因となります。例えば、必要な設定に対する誤った、サポートされていない、設定が見つかりません。 をデプロイする前に、設定を更新してこれらのエラーを解決する必要があります。

### ビルドステージ

| エラーコード | ビルド手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 2 |  | に書き込めません `./app/etc/env.php` ファイル | 配置スクリプトは、 `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 3 |  | で設定が定義されていません `schema.yaml` ファイル | 設定が `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 4 |  | を解析できませんでした `.magento.env.yaml` ファイル | The `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文をチェックし、エラーを修正します。 |
| 5 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 6 |  | を読み取れません `.schema.yaml` ファイル | を読み取れません `./vendor/magento/ece-tools/config/magento.env.yaml` ファイル。 ファイル権限を確認し、再デプロイ (`magento-cloud environment:redeploy`) をクリックします。 |
| 7 | refresh-modules | に書き込めません `./app/etc/config.php` ファイル | デプロイメントスクリプトは、 `/app/etc/config.php` ファイル。 ファイルシステムの権限を確認します。 |
| 8 | validate-config | を読み取れません `composer.json` ファイル | を読み取れません `./composer.json` ファイル。 ファイルの権限を確認します。 |
| 9 | validate-config | Composer.json に必要な自動読み込みセクションがありません | 必須 `autoload` セクションが `composer.json` ファイル。 自動読み込みセクションと `composer.json` ファイルをクラウドテンプレートに追加し、見つからない設定を追加します。 |
| 10 | validate-config | ファイル `.magento.env.yaml` には、スキーマで宣言されていないオプション、または無効な値またはステージで設定されたオプションが含まれています | The `./.magento.env.yaml` ファイルに無効な設定が含まれています。 詳細については、エラーログを確認してください。 |
| 11 | refresh-modules | コマンドが失敗しました： `/bin/magento module:enable --all` | 実行してみる `composer update` ローカルで使用できます。 次に、更新されたをコミットしてプッシュします `composer.lock` ファイル。 また、 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 12 | apply-patches | パッチを適用できませんでした |  |
| 13 | set-report-dir-nesting-level | ファイルに書き込めません `/pub/errors/local.xml` |  |
| 14 | copy-sample-data | サンプルデータファイルをコピーできませんでした |  |
| 15 | compile-di | コマンドが失敗しました： `/bin/magento setup:di:compile` | 次を確認します。 `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` into `.magento.env.yaml` コマンド出力の詳細は、を参照してください。 |
| 16 | dump-autoload | コマンドが失敗しました： `composer dump-autoload` | The `composer dump-autoload` コマンドが失敗しました。 次を確認します。 `cloud.log` を参照してください。 |
| 17 | ランベラー | 実行するコマンド `Baler` （JavaScript のバンドルに失敗しました） | 次を確認します。 `SCD_USE_BALER` 環境変数を使用して、Baler モジュールが設定され、JS のバンドルが有効になっていることを確認します。 Baler モジュールが不要な場合は、 `SCD_USE_BALER: false`. |
| 18 | compress-static-content | 必要なユーティリティが見つかりませんでした（タイムアウト、bash） |  |
| 19 | deploy-static-content | コマンド `/bin/magento setup:static-content:deploy` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 20 | compress-static-content | 静的コンテンツの圧縮に失敗しました | 次を確認します。 `cloud.log` を参照してください。 |
| 21 | backup-data: static-content | 静的コンテンツを `init` directory | 次を確認します。 `cloud.log` を参照してください。 |
| 22 | backup-data: writable-dirs | 書き込み可能なディレクトリの一部をにコピーできませんでした `init` directory | 書き込み可能なディレクトリをにコピーできませんでした `./init` フォルダー。 ファイルシステムの権限を確認します。 |
| 23 |  | ロガーオブジェクトを作成できません |  |
| 24 | backup-data: static-content | をクリーンアップできませんでした `./init/pub/static/` directory | クリーンアップに失敗しました `./init/pub/static` フォルダー。 ファイルシステムの権限を確認します。 |
| 25 |  | Composer パッケージが見つかりません | Adobe Commerceアプリケーションのバージョンを GitHub リポジトリから直接インストールした場合は、 `DEPLOYED_MAGENTO_VERSION_FROM_GIT` 環境変数が設定されている。 |
| 26 | validate-config | Adobe CommerceおよびMagento2.4 以降のバージョンでサポートされなくなったMagento Open SourceBraintreeモジュール設定を削除します。 | Braintreeモジュールのサポートは、Commerce 2.4.0 以降には含まれなくなりました。 CONFIG__STORES__DEFAULT__PAYMENT__CHANNEL 変数を `.magento.app.yaml` ファイル。 Braintreeの支払いをサポートする場合は、代わりにCommerce Marketplaceの正式な内線を使用します。 |

### ステージのデプロイ

| エラーコード | デプロイ手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 101 | プリデプロイ：キャッシュ | キャッシュ設定が正しくありません（ポートまたはホストがありません） | キャッシュ設定に必要なパラメーターがありません `server` または `port`. 次を確認します。 `cloud.log` を参照してください。 |
| 102 |  | に書き込めません `./app/etc/env.php` ファイル | 配置スクリプトは、 `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 103 |  | で設定が定義されていません `schema.yaml` ファイル | 設定が `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 104 |  | を解析できませんでした `.magento.env.yaml` ファイル | 設定が `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 105 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 106 |  | を読み取れません `.schema.yaml` ファイル |  |
| 107 | pre-deploy: clean-redis-cache | Redis キャッシュをクリーンアップできませんでした | Redis キャッシュをクリーンアップできませんでした。 Redis キャッシュの設定が正しく、Redis サービスが使用可能であることを確認します。 詳しくは、 [Redis サービスの設定](../services/redis.md). |
| 108 | プリデプロイ：set-production-mode | コマンド `/bin/magento maintenance:enable` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 109 | validate-config | データベース設定が正しくありません | 以下を確認します。 `DATABASE_CONFIGURATION` 環境変数が正しく設定されている。 |
| 110 | validate-config | 間違ったセッション設定 | 以下を確認します。 `SESSION_CONFIGURATION` 環境変数が正しく設定されている。 設定には、少なくとも `save` パラメーター。 |
| 111 | validate-config | 不正な検索設定 | 以下を確認します。 `SEARCH_CONFIGURATION` 環境変数が正しく設定されている。 設定には、少なくとも `engine` パラメーター。 |
| 112 | validate-config | リソース設定が正しくありません | 以下を確認します。 `RESOURCE_CONFIGURATION` 環境変数が正しく設定されている。 設定には少なくとも次を含める必要があります `connection` パラメーター。 |
| 113 | validate-config:elasticsuite-integrity | ElasticSuite がインストールされていますが、Elasticsearchサービスは使用できません | 以下を確認します。 `SEARCH_CONFIGURATION` 環境変数が正しく設定され、環境サービスが使用可能であることをElasticsearchします。 |
| 114 | validate-config:elasticsuite-integrity | ElasticSuite がインストールされていますが、別の検索エンジンが使用されています | ElasticSuite がインストールされていますが、別の検索エンジンが設定されています。 を更新します。 `SEARCH_CONFIGURATION` 環境変数を使用してElasticsearchを有効にし、 `services.yaml` ファイル。 |
| 115 |  | データベースクエリの実行に失敗しました |  |
| 116 | install-update：セットアップ | コマンド `/bin/magento setup:install` 失敗 | 次を確認します。 `cloud.log` および `install_upgrade.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 117 | install-update: config-import | コマンド `app:config:import` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 118 |  | 必要なユーティリティが見つかりませんでした（タイムアウト、bash） |  |
| 119 | install-update: deploy-static-content | コマンド `/bin/magento setup:static-content:deploy` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 120 | compress-static-content | 静的コンテンツの圧縮に失敗しました | 次を確認します。 `cloud.log` を参照してください。 |
| 121 | deploy-static-content:generate | デプロイされたバージョンを更新できません | 次を更新できません： `./pub/static/deployed_version.txt` ファイル。 ファイルシステムの権限を確認します。 |
| 122 | clean-static-content | 静的コンテンツファイルをクリーンアップできませんでした |  |
| 123 | install-update: split-db | コマンド `/bin/magento setup:db-schema:split` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 124 | clean-view-preprocessed | をクリーンアップできませんでした `var/view_preprocessed` フォルダー | をクリーンアップできません `./var/view_preprocessed` フォルダー。 ファイルシステムの権限を確認します。 |
| 125 | install-update: reset-password | 次を更新できませんでした： `/var/credentials_email.txt` ファイル | 次を更新できませんでした： `/var/credentials_email.txt` ファイル。 ファイルシステムの権限を確認します。 |
| 126 | install-update: update | コマンド `/bin/magento setup:upgrade` 失敗 | 次を確認します。 `cloud.log` および `install_upgrade.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 127 | クリーンキャッシュ | コマンド `/bin/magento cache:flush` 失敗 | 次を確認します。 `cloud.log` を参照してください。 コマンド出力の詳細については、 `VERBOSE_COMMANDS: '-vvv'` オプションを `.magento.env.yaml` ファイル。 |
| 128 | disable-maintenance-mode | コマンド `/bin/magento maintenance:disable` 失敗 | 次を確認します。 `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` into `.magento.env.yaml` コマンド出力の詳細は、を参照してください。 |
| 129 | install-update: reset-password | リセットパスワードテンプレートを読み取れません |  |
| 130 | install-update: cache_type | コマンドが失敗しました： `php ./bin/magento cache:enable` | コマンド `php ./bin/magento cache:enable` は、 Adobe Commerceがインストールされている場合にのみ実行されますが、 `./app/etc/env.php` デプロイメントの開始時にファイルが存在しなかったか、空でした。 次を確認します。 `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` into `.magento.env.yaml` コマンド出力の詳細は、を参照してください。 |
| 131 | install-update | The `crypt/key` キーの値が `./app/etc/env.php` ファイルまたは `CRYPT_KEY` クラウド環境変数 | このエラーは、 `./app/etc/env.php` ファイルが存在しない (Adobe Commerceのデプロイメントが開始したとき、または存在しない ) `crypt/key` の値が未定義です。 別の環境からデータベースを移行した場合は、その環境から暗号キーの値を取得します。 次に、 [CRYPT_KEY](../environment/variables-deploy.md#crypt_key) クラウド環境変数を現在の環境で使用します。 詳しくは、 [Magento暗号化キーを追加](../development/authentication-keys.md). 誤って `./app/etc/env.php` ファイルを復元するには、次のコマンドを使用して、以前のデプロイメントから作成したバックアップファイルから復元します。 `./vendor/bin/ece-tools backup:restore` CLI コマンド。」 |
| 132 |  | Elasticsearchサービスに接続できません | 有効なElasticsearch資格情報を確認し、サービスが実行中であることを確認します |
| 137 |  | OpenSearch サービスに接続できません | 有効な OpenSearch 資格情報を確認し、サービスが実行中であることを確認します |
| 133 | validate-config | Adobe CommerceまたはMagento2.4 以降のバージョンでサポートされなくなったMagento Open SourceBraintreeモジュール設定を削除します。 | Braintreeモジュールのサポートは、Adobe CommerceまたはMagento Open Source2.4.0 以降には含まれなくなりました。 CONFIG__STORES__DEFAULT__PAYMENT__CHANNEL 変数を `.magento.app.yaml` ファイル。 Braintreeのサポートには、代わりに、Commerce Marketplaceの正式なBraintree支払い拡張を使用します。 |
| 134 | validate-config | Adobe CommerceおよびMagento Open Source2.4.0 にはElasticsearchサービスがインストールされている必要があります | インストールElasticsearchサービス |
| 138 | validate-config | Adobe CommerceおよびMagento Open Source2.4.4 では、OpenSearch またはElasticsearchサービスがインストールされている必要があります | OpenSearch サービスをインストールする |
| 135 | validate-config | 検索エンジンは、Adobe Commerceの「Elasticsearch」に設定され、Magento Open Sourceが 2.4.0 以上に設定されている必要があります。 | SEARCH_CONFIGURATION 変数で `engine` オプション。 設定されている場合は、このオプションを削除するか、値を&quot;elasticsearch&quot;に設定します。 |
| 136 | validate-config | Split Database は、Adobe CommerceとMagento Open Source2.5.0 から削除されました。 | 分割データベースを使用する場合は、元のデータベースに戻すか、単一のデータベースに移行するか、別の方法を使用する必要があります。 |
| 139 | validate-config | 不正な検索エンジン | このAdobe CommerceまたはMagento Open Sourceのバージョンは OpenSearch をサポートしていません。 バージョン 2.3.7-p3、2.4.3-p2 以降を使用する必要があります |

### デプロイ後のステージ

| エラーコード | デプロイ後の手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 201 | is-deploy-failed | ステージのデプロイに失敗しました |  |
| 202 |  | The `./app/etc/env.php` ファイルは書き込み可能ではありません | 配置スクリプトは、 `/app/etc/env.php` ファイル。 ファイルシステムの権限を確認します。 |
| 203 |  | で設定が定義されていません `schema.yaml` ファイル | 設定が `./vendor/magento/ece-tools/config/schema.yaml` ファイル。 設定変数名が正しく、定義されていることを確認します。 |
| 204 |  | を解析できませんでした `.magento.env.yaml` ファイル | The `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文をチェックし、エラーを修正します。 |
| 205 |  | を読み取れません `.magento.env.yaml` ファイル | ファイルの権限を確認します。 |
| 206 |  | を読み取れません `.schema.yaml` ファイル |  |
| 207 | ウォームアップ | 一部のページのウォームアップに失敗しました |  |
| 208 | time-to-firs-byte | 最初のバイトまでの時間のテストに失敗しました (TTFB) |  |
| 227 | クリーンキャッシュ | コマンド `/bin/magento cache:flush` 失敗 | 次を確認します。 `cloud.log` を参照してください。 追加 `VERBOSE_COMMANDS: '-vvv'` into `.magento.env.yaml` コマンド出力の詳細は、を参照してください。 |

### 一般

| エラーコード | 一般的な手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 243 |  | 設定が `schema.yaml` ファイル | 設定変数名が正しく、定義されていることを確認します。 |
| 244 |  | を解析できませんでした `.magento.env.yaml` ファイル | The `./.magento.env.yaml` ファイル形式が無効です。 YAML パーサーを使用して構文をチェックし、エラーを修正します。 |
| 245 |  | を読み取れません `.magento.env.yaml` ファイル | を読み取れません `./.magento.env.yaml` ファイル。 ファイルの権限を確認します。 |
| 246 |  | を読み取れません `.schema.yaml` ファイル |  |
| 247 |  | イベンティング用のモジュールを生成できません | 次を確認します。 `cloud.log` を参照してください。 |
| 248 |  | イベンティング用のモジュールを有効にできません | 次を確認します。 `cloud.log` を参照してください。 |

## 警告エラー

警告エラーは、クラウドインフラストラクチャプロジェクトの設定に関する Commerce の問題を示しています。例えば、サイトの操作に影響を与えるオプション機能の設定が正しくない、非推奨（廃止予定）、サポートされていない、などです。 警告を出してもデプロイメントエラーは発生しませんが、警告メッセージを確認し、設定を更新して解決する必要があります。

### ビルドステージ

| エラーコード | ビルド手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 1001 | validate-config | ファイルapp/etc/config.phpが存在しません |  |
| 1002 | validate-config | ./build_options.iniファイルはサポートされなくなりました |  |
| 1003 | validate-config | 共有設定ファイルにモジュールセクションがありません |  |
| 1004 | validate-config | この設定は、このバージョンのコマースと互換性がありません |  |
| 1005 | validate-config | 無視された SCD オプション |  |
| 1006 | validate-config | 設定された状態は理想的ではありません |  |
| 1007 | ランベラー | Baler JS のバンドル化は使用できません |  |

### ステージのデプロイ

| エラーコード | デプロイ手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 2001 | プリデプロイ：cache | キャッシュは、利用できない Redis サービス用に設定されています。 設定は無視されます。 |  |
| 2002 | validate-config | 設定された状態は理想的ではありません |  |
| 2003 | validate-config | エラーレポートのディレクトリネストレベルの値が設定されていません |  |
| 2004 | validate-config | の設定が無効です。/pub/errors/local.xmlファイルを参照してください。 |  |
| 2005 | validate-config | 管理者データは、初回インストール時にのみ管理者ユーザーを作成するために使用されます。 管理者データに対する変更は、アップグレードプロセス中は無視されます。 | 初回のインストール後、管理者データを設定から削除できます。 |
| 2006 | validate-config | 管理者の電子メールが設定されていないので、管理者ユーザーは作成されませんでした | インストール後、管理者ユーザーを手動で作成できます。ssh を使用して環境に接続します。 次に、 `bin/magento admin:user:create` コマンドを使用します。 |
| 2007 | validate-config | php バージョンを推奨バージョンに更新 |  |
| 2008 | validate-config | Adobe CommerceおよびMagento Open Source2.1 では、Solr のサポートは廃止されました。 |  |
| 2009 | validate-config | Solr は、Adobe CommerceおよびMagento Open Source2.2 以降ではサポートされなくなりました。 |  |
| 2010 | validate-config | Elasticsearchサービスはインフラストラクチャ層にインストールされていますが、検索エンジンとしては使用されていません。 | リソースの使用を最適化するには、Elasticsearchサービスをインフラストラクチャレイヤーから削除することを検討してください。 |
| 2011 | validate-config | インフラストラクチャレイヤー上のElasticsearchサービスバージョンは、Adobe Commerceアプリケーションで使用されている elasticsearch/elasticsearch モジュールの現在のバージョンと互換性がありません。 |  |
| 2012 | validate-config | 現在の設定は、このバージョンのAdobe Commerceと互換性がありません |  |
| 2013 | validate-config | SCD オプションは無視されました。ビルドフェーズでデプロイプロセスが実行されませんでした。 |  |
| 2014 | validate-config | 設定に非推奨の変数または値が含まれています |  |
| 2015 | validate-config | 環境設定が無効です |  |
| 2016 | validate-config | JSON タイプの設定をデコードできません |  |
| 2017 | validate-config | 現在の設定は、このバージョンのAdobe Commerceと互換性がありません |  |
| 2018 | validate-config | 一部のサービスが EOL に合格しました |  |
| 2019 | validate-config | MySQL 検索設定オプションは非推奨（廃止予定）となりました | 代わりに、Elasticsearchを使用します。 |
| 2029 | validate-config | 分割データベースは、Adobe CommerceおよびMagento Open Source2.4.2 で廃止され、2.5 で削除されます。 | 分割データベースを使用する場合は、1 つのデータベースに戻す、または 1 つのデータベースに移行する計画を開始するか、別の方法を使用する必要があります。 |
| 2020 | install-update | Adobe Commerceのインストールが完了しましたが、 `app/etc/env.php` 設定ファイルが見つからないか、空です。 | 必要なデータは、環境設定および.magento.env.yaml ファイルから復元されます。 |
| 2021 | install-update:db-connection | カスタム接続を使用する分割データベースの場合 |  |
| 2022 | install-update:db-connection | スレーブ接続と互換性のないデータベース構成に変更しました。 |  |
| 2023 | install-update:split-db | 分割データベースの有効化はスキップされます。 |  |
| 2024 | install-update:split-db | SPLIT_DB 変数に分割接続タイプの構成がありません。 |  |
| 2025 | install-update:split-db | スレーブ接続が設定されていません。 |  |
| 2026 | pre-deploy:restore-writable-dirs | ビルドフェーズ中に生成された一部のデータをマウントされたディレクトリに復元できませんでした | 次を確認します。 `cloud.log` を参照してください。 |
| 2027 | validate-config:mage-mode-variable | MAGE_MODE 環境変数のモード値はサポートされていません | MAGE_MODE 環境変数を削除するか、値を&quot;production&quot;に変更します。 クラウドインフラストラクチャ上のAdobe Commerceは、「実稼動」モードのみをサポートします。 |
| 2028 | リモートストレージ | リモートストレージを有効にできませんでした。 | リモートストレージの資格情報を確認します。 |
| 2030 | validate-config | Elasticsearchサービスと OpenSearch サービスは、どちらもインフラストラクチャ層にインストールされています。 Adobe CommerceおよびMagento Open Source2.4.4 以降では、デフォルトで OpenSearch が使用されます | リソースの使用を最適化するには、Elasticsearchまたは OpenSearch サービスをインフラストラクチャレイヤーから削除することを検討してください。 |

### デプロイ後のステージ

| エラーコード | デプロイ後の手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 3001 | validate-config | Adobe Commerceでデバッグのログが有効になっています | ディスク容量を節約するには、実稼動環境のデバッグログを有効にしないでください。 |
| 3002 | ウォームアップ | ストア URL を取得できません |  |
| 3003 | ウォームアップ | ストア URL を取得できません |  |
| 3004 | バックアップ | バックアップファイルを作成できません |  |

### 一般

| エラーコード | 一般的な手順 | エラーの説明（タイトル） | 推奨アクション |
| - | - | - | - |
| 4001 |  | システムプロセッサ数を取得できません： |  |
