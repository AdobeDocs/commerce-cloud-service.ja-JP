---
source-git-commit: b08443d937dfc18120daa0d6a1277b9c7bca67aa
workflow-type: tm+mt
source-wordcount: '829'
ht-degree: 0%

---
# クラウドスニペット

## Elasticsearch警告 {#elasticsearch-support}

>[!WARNING]
>
>Elasticsearch 7.11 以降は、クラウドインフラストラクチャー上のAdobe Commerceではサポートされていません。 Adobe Commerce バージョン 2.3.7 ～ p3、2.4.3 ～ p2、および 2.4.4 以降では、OpenSearch サービスをサポートしています。 オンプレミスのインストールでは、引き続きElasticsearchがサポートされます。

## 統合の強化 {#enhanced-integration-envs}

>[!NOTE]
>
>2020 年 6 月 5 日（PT）より前にプロビジョニングされたプロジェクトには、複数の小規模な統合環境がありました。 テストおよび開発に大規模な統合環境が必要な場合は、拡張統合環境へのアップグレードをリクエストします。 を参照してください。 [統合環境リクエスト](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/announcements/commerce-announcements/integration-environment-enhancement-request-pro-and-starter.html) の記事 _Adobe Commerceヘルプセンター_ を参照してください。

## 結合オプション {#merge-options}

デフォルトでは、デプロイメントプロセスはのすべての設定を `env.php` ファイル。ただし、すべての値を上書きせずに、サービス設定の 1 つ以上の値を結合することを選択できます。

を `_merge` 次のいずれかのオプションを選択します。

- `true`—**結合** 設定済みのサービス値と環境変数の値。
- `false`—**上書き** 設定済みのサービス値と環境変数の値。

## プライベートリポジトリ {#private-repository}

>[!NOTE]
>
>Adobeでは、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにプライベートリポジトリを使用して、拡張機能や機密設定などの専有情報または開発作業をすべて保護することを強くお勧めします。

## Pro セルフサービスの警告 {#pro-self-service-warning}

>[!WARNING]
>
>一部 **Pro プロジェクト** でルート設定を更新するためにサポートチケットを必要とする `routes.yaml` のファイルと cron 設定 `.magento.app.yaml` ファイル。 Adobe環境で YAML 設定ファイルを更新およびテストしたあと、変更内容をステージング環境にデプロイすることをお勧めします。 再デプロイ後に変更がステージングサイトに適用されず、ログに関連するエラーメッセージがない場合は、次のようになります **が** [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 試行された設定変更を説明します。 更新された YAML 設定ファイルをチケットに含めます。

## プロサービスサポート {#pro-update-service}

>[!TIP]
>Pro プロジェクトの場合は、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をインストールまたは更新するには [サービス](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/service/services-yaml.html) 。対象： `Staging` および `Production` 環境のみ。
>
>必要なサービス変更を示し、更新済みを含める `.magento.app.yaml` および `services.yaml` ファイルを開き、PHP のバージョンをチケットに書き込みます。 PHP のバージョン、拡張機能、または環境設定のセルフサービスの変更については、を参照してください。 [PHP 設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/app/php-settings.html) 。対象： _アプリケーション設定_.
>
>に変更を加えた場合 _ライブ_ 実稼動環境（**Pro のみ**）、クラウドインフラストラクチャチームがリソースをマーシャリングし、安全なアップグレードを実施するのに十分な時間を確保するために、少なくとも 48 時間の注意事項を提供する必要があります。

## Pro バックアップ {#pro-backups}

>[!TIP]
>
>ステージング環境および実稼動環境では、次の操作が必要です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) チケット内の日付、時刻、タイムゾーンを記録する特定のバックアップを取得します。
>
>Adobeが実行 **ではない** 自動バックアップから環境を復元します。 参照： [ステージング環境または実稼動環境から DB スナップショットを復元](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/restore-a-db-snapshot-from-staging-or-production.html) ステージングスナップショットまたは実稼動スナップショットを復元する方法を選択する際に役立ちます。

## 再配置警告 {#redeploy-warning}

>[!WARNING]
>
>デプロイメントプロセスは、環境のマージ、プッシュまたは同期化を実行するとき、または手動で再デプロイメントをトリガーするときに開始され、その間に [!DNL Commerce] アプリケーションはメンテナンス モードです。 実稼動環境の場合、Adobeは、サービスが中断されないように、この作業をピーク外の時間に完了することをお勧めします。

## ルートプレースホルダー {#route-placeholder}

>[!NOTE]
>
>次のルート設定例では、プレースホルダーを含むルートテンプレートを使用します。 この `{default}` プレースホルダーは、サイトに設定されたデフォルトのドメインを表します。 プロジェクトに複数のドメインがある場合は、を使用します `{all}` デフォルトドメインとすべてのエイリアスのルーティングを設定するためのプレースホルダー。 参照： [ルートの設定](/help/cloud-guide/routes/routes-yaml.md).

## SCD タイミング {#scd-timing-warning}

>[!WARNING]
>
>カスタムテーマファイルがないなど、デプロイ後のアプリケーションに静的コンテンツファイルに関する問題がある場合は、予想最大実行時間を 900 秒以上に増やします。

## シナリオベースのデプロイメント {#scenarios}

>[!NOTE]
>
>（を使用） [!DNL ECE-Tools] 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのビルド、デプロイ、デプロイ後のプロセスをカスタマイズできます。 参照： [シナリオベースのデプロイメント](/help/cloud-guide/deploy/scenario-based.md).

## サービス指示 {#service-instruction}

Pro 統合環境およびスターター環境でのサービス設定については、以下の手順に従ってください。 `master` 分岐。

>[!NOTE]
>
>[Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) pro 実稼動環境およびステージング環境でサービス設定を変更する場合。

## サービスの変更 {#service-change-tip}

>[!TIP]
>
>サービスの初期セットアップ後、 `services.yaml` および `.magento.app.yaml` 設定ファイル。 参照： [サービスバージョンの変更](/help/cloud-guide/services/services-yaml.md#change-service-version) サービスのアップグレードまたはダウングレードのガイダンス。

## スタックしたデプロイメントのヒント {#stuck-deployment-tip}

>[!TIP]
>
>スタックしたデプロイメントのヘルプについては、を使用してください [Adobe Commerce導入のトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/deployment/magento-deployment-troubleshooter.html) が含まれる _Commerceヘルプセンター_.

## ECE-Tools のアップデート {#ece-tools-package}

>[!NOTE]
>
>を含まないバージョンのAdobe Commerceをクラウドインフラストラクチャ上で使用する場合 `ece-tools` パッケージ化する場合は、次を実行する必要があります。 [1 回限りのアップグレード](/help/cloud-guide/dev-tools/install-package.md) をクラウドプロジェクトに追加して、非推奨（廃止予定）のパッケージを削除します。 現在を使用している場合 `ece-tools` パッケージを更新する必要があります。次を参照してください。 [ECE-Tools パッケージの更新](/help/cloud-guide/dev-tools/update-package.md).

## アップグレードのヒント {#upgrade-tip}

>[!TIP]
>
>アップグレードまたはパッチ適用プロセスを開始する前に、統合環境からアクティブなブランチを作成し、新しいブランチをローカルワークステーションにチェックアウトします。 分岐をアップグレードまたはパッチプロセスに割り当てると、進行中の作業への干渉を回避できます。

<!-- Fastly-related snippets begin -->

## 管理者ログイン {#admin-login-step}

1. [ログイン](/help/get-started/onboarding.md#access-your-admin-panel) を管理者に送信します。

## カスタム VCL スニペットの配備の自動化 {#automate-vcl-snippet-deployment}

>[!NOTE]
>
>カスタム VCL スニペットを手動でアップロードする代わりに、スニペットを `$MAGENTO_CLOUD_APP_DIR/var/vcl_snippets_custom` 環境内のディレクトリ。 クリックすると、このディレクトリ内のスニペットが自動的にアップロードされます _vcl の Fastly へのアップロード_ Commerce Admin. 参照： [カスタム VCL スニペットの導入を自動化](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/CUSTOM-VCL-SNIPPETS.md#automated-custom-vcl-snippets-deployment) Magento 2 用 Fastly CDN モジュールのドキュメントを参照してください。

<!-- Fastly-related snippets end -->
