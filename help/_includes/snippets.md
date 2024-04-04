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
>Elasticsearch7.11 以降は、クラウドインフラストラクチャ上のAdobe Commerceではサポートされていません。 Adobe Commerceのバージョン 2.3.7-p3、2.4.3-p2、2.4.4 以降では、OpenSearch サービスがサポートされています。 オンプレミスインストールでは、引き続きElasticsearchがサポートされます。

## 統合の強化 {#enhanced-integration-envs}

>[!NOTE]
>
>2020 年 6 月 6 日より前にプロビジョニングされたプロジェクトでは、複数の、より小さい統合環境が存在していました。 テストおよび開発に大規模な統合環境が必要な場合は、拡張統合環境へのアップグレードをリクエストします。 詳しくは、 [統合環境リクエスト](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/announcements/commerce-announcements/integration-environment-enhancement-request-pro-and-starter.html) 記事内の _Adobe Commerce Help Center_ 」を参照してください。

## 結合オプション {#merge-options}

デフォルトでは、デプロイメントプロセスは、 `env.php` ファイルに書き込みます。ただし、すべての値を上書きせずに、サービス構成の 1 つ以上の値をマージするように選択できます。

を設定します。 `_merge` オプションを次のいずれかに設定します。

- `true`—**結合** 設定済みのサービス値と環境変数の値。
- `false`—**上書き** 設定済みのサービス値と環境変数の値。

## プライベートリポジトリ {#private-repository}

>[!NOTE]
>
>Adobeでは、拡張機能や機密設定などの独自の情報や開発作業を保護するために、クラウドインフラストラクチャプロジェクト上のAdobe Commerceにプライベートリポジトリを使用することを強くお勧めします。

## セルフサービスに関する警告 {#pro-self-service-warning}

>[!WARNING]
>
>一部 **プロプロジェクト** でルート設定を更新するには、サポートチケットが必要です。 `routes.yaml` ファイルと、 `.magento.app.yaml` ファイル。 Adobeでは、統合環境で YAML 設定ファイルを更新およびテストしてから、ステージング環境に変更をデプロイすることをお勧めします。 再デプロイ後に変更がステージングサイトに適用されず、ログに関連するエラーメッセージが表示されない場合は、 **MUST** [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) これは、試行された構成の変更を示します。 更新された YAML 設定ファイルをチケットに含めます。

## Pro Services サポート {#pro-update-service}

>[!TIP]
>Pro プロジェクトの場合、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) インストールまたは更新する [services](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/service/services-yaml.html) in `Staging` および `Production` 環境のみ。
>
>必要なサービスの変更を示し、更新した `.magento.app.yaml` および `services.yaml` ファイルを作成し、PHP のバージョンをチケット内に記述します。 PHP のバージョン、拡張機能、または環境設定に対するセルフサービスの変更については、 [PHP 設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/app/php-settings.html) in _アプリケーション設定_.
>
>の変更 _live_ 実稼動環境 (**Pro のみ**) を使用する場合、Cloud Infrastructure チームがリソースをマーシャリングし、安全なアップグレードを実行するのに十分な時間を確保できるよう、最低 48 時間の通知を提供する必要があります。

## プロバックアップ {#pro-backups}

>[!TIP]
>
>Pro ステージング環境および実稼動環境では、次の操作を行う必要があります。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) ：チケット内の日付、時刻、タイムゾーンを示す特定のバックアップを取得します。
>
>Adobeが実行する **not** 自動バックアップから環境を復元します。 詳しくは、 [DB スナップショットをステージングまたは実稼動から復元する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/how-to/restore-a-db-snapshot-from-staging-or-production.html) を参照して、ステージングまたは実稼動のスナップショットを復元する方法を選択してください。

## 再デプロイの警告 {#redeploy-warning}

>[!WARNING]
>
>デプロイメントプロセスは、環境の結合、プッシュまたは同期を実行するとき、または手動で再デプロイメントをトリガーするときに開始します。この間、 [!DNL Commerce] アプリケーションはメンテナンスモードです。 実稼動環境の場合、Adobeは、サービスの中断を避けるために、オフピーク時にこの作業を完了することを推奨します。

## ルートプレースホルダー {#route-placeholder}

>[!NOTE]
>
>次のルート設定の例では、プレースホルダ付きのルートテンプレートを使用します。 The `{default}` プレースホルダーは、サイトに設定されたデフォルトのドメインを表します。 プロジェクトに複数のドメインがある場合は、 `{all}` デフォルトのドメインとすべてのエイリアスのルーティングを構成するためのプレースホルダーです。 詳しくは、 [ルートの設定](/help/cloud-guide/routes/routes-yaml.md).

## SCD のタイミング {#scd-timing-warning}

>[!WARNING]
>
>カスタムテーマファイルの欠落など、デプロイ後のアプリケーションの静的コンテンツファイルに問題がある場合は、予想される最大実行時間を 900 秒以上に増やします。

## シナリオベースのデプロイメント {#scenarios}

>[!NOTE]
>
>を使用 [!DNL ECE-Tools] 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、Adobe Commerce on cloud infrastructure プロジェクトのビルド、デプロイ、デプロイ後のプロセスをカスタマイズできます。 詳しくは、 [シナリオベースのデプロイメント](/help/cloud-guide/deploy/scenario-based.md).

## サービス指示 {#service-instruction}

次の手順を、Pro 統合環境および Starter 環境でのサービス設定に使用します。 `master` 分岐。

>[!NOTE]
>
>[Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) をクリックして、Pro 実稼動環境とステージング環境のサービス設定を変更します。

## サービスの変更 {#service-change-tip}

>[!TIP]
>
>初期のサービス設定後、インストール済みサービスのソフトウェアバージョンを変更するには、 `services.yaml` および `.magento.app.yaml` 設定ファイル。 詳しくは、 [サービスバージョンの変更](/help/cloud-guide/services/services-yaml.md#change-service-version) サービスのアップグレードまたはダウングレードに関するガイダンス。

## デプロイメントのヒントが動作しません {#stuck-deployment-tip}

>[!TIP]
>
>停止したデプロイメントに関するヘルプについては、 [Adobe Commerceデプロイメントトラブルシューター](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/deployment/magento-deployment-troubleshooter.html) （内） _Commerce ヘルプセンター_.

## ECE-Tools に更新 {#ece-tools-package}

>[!NOTE]
>
>を含まないバージョンのAdobe Commerceをクラウドインフラストラクチャ上で使用する場合 `ece-tools` パッケージを作成した場合は、次の操作を実行する必要があります。 [1 回限りのアップグレード](/help/cloud-guide/dev-tools/install-package.md) をクラウドプロジェクトに追加して、非推奨のパッケージを削除します。 現在 `ece-tools` パッケージを更新する必要があります。詳しくは、 [ECE-Tools パッケージを更新します。](/help/cloud-guide/dev-tools/update-package.md).

## アップグレードのヒント {#upgrade-tip}

>[!TIP]
>
>アップグレードまたはパッチ適用プロセスを開始する前に、統合環境からアクティブなブランチを作成し、新しいブランチをローカルワークステーションにチェックアウトします。 アップグレードまたはパッチプロセスのブランチを指定すると、進行中の作業への干渉を回避できます。

<!-- Fastly-related snippets begin -->

## 管理者ログイン {#admin-login-step}

1. [ログイン](/help/get-started/onboarding.md#access-your-admin-panel) 管理者に問い合わせます。

## カスタム VCL スニペットの導入を自動化 {#automate-vcl-snippet-deployment}

>[!NOTE]
>
>カスタム VCL スニペットを手動でアップロードする代わりに、にスニペットを追加できます。 `$MAGENTO_CLOUD_APP_DIR/var/vcl_snippets_custom` ディレクトリ内に存在します。 このディレクトリ内のスニペットは、 _VCL を Fastly にアップロード_ （コマース管理）をクリックします。 詳しくは、 [カスタム VCL スニペットのデプロイメントの自動化](https://github.com/fastly/fastly-magento2/blob/master/Documentation/Guides/CUSTOM-VCL-SNIPPETS.md#automated-custom-vcl-snippets-deployment) ( Fastly CDN モジュールのMagento2 ドキュメント )。

<!-- Fastly-related snippets end -->
