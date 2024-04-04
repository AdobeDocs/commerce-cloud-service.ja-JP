---
title: プロジェクトのアップグレードのベストプラクティス
description: プロジェクトファイルをアップグレードする際のベストプラクティスの一覧を参照してください。
feature: Cloud, Best Practices, Upgrade
exl-id: 7d0a2627-e4c5-46b4-9e6c-24d20fa4f92f
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '442'
ht-degree: 0%

---

# プロジェクトのアップグレードのベストプラクティス

ビルドとデプロイメントのベストプラクティスに従い、 [アップグレードとパッチ](../development/commerce-version.md) ワークフローを使用して、アプリケーションをアップグレードします。 次のガイドラインに従って、アップグレードおよびアップグレード後の作業を計画します。

- **プロジェクトをバックアップ**- Adobe Commerceおよびその他のサードパーティまたはカスタムの拡張機能をアップグレードする前に、統合、ステージング、実稼動の各環境でデータベースをバックアップします。 詳しくは、 [データベースのバックアップ](../development/commerce-version.md#project-backup).

- **互換性の問題を確認**-

   - すべてのカスタムテーマが新しいAdobe Commerceバージョンと互換性があることを確認する

   - サードパーティの拡張機能とカスタム拡張機能をアップグレードした後、 `magento-cloud local:build` コマンドを使用して、デプロイする前に Composer の依存関係を検証します。

   - Adobe Commerceのリリースノートと拡張機能ドキュメントを確認して、Adobe Commerceのバージョンと拡張機能のアップグレードに関連する既知の機能上の問題やバグに対処するために必要な回避策や設定の変更を実装していることを確認します。

   - インストールされているサービスのバージョンが、新しいAdobe Commerceのバージョンと互換性があることを確認し、必要に応じてサービスをアップグレードします。 詳しくは、 [サービス](../services/services-yaml.md).

   - Adobe Commerceのバージョンと拡張機能のアップデートで発生した問題に対処するために、データベースをテストします。

   - リモート環境に展開する前に、環境固有の設定に必要な更新を行います。

   - 検索サービスのバージョンが PHP クライアントのバージョンと互換性があることを確認します。 詳しくは、 [設定Elasticsearch](../services/elasticsearch.md) または [OpenSearch の設定](../services/opensearch.md).

- **リモート環境でのデータベース接続と使用可能なストレージの確認**-

   - SSH を使用してリモートサーバーにログインし、MySQL データベースへの接続を確認します。 詳しくは、 [データベースに接続](../services/mysql.md#connect-to-the-database).

   - リモート環境で使用可能なストレージを確認します。 `disk free` 」コマンドを使用して、クラウド環境上の使用可能なディスク領域を表示および管理できます。 詳しくは、 [ディスク容量の管理](../storage/manage-disk-space.md).

      - アップグレードされたデータベースのサイズを確認し、 `services.yaml` ファイルに十分なディスク領域が割り当てられています。

      - ディスク領域を解放します。キャッシュをクリアし、次の操作を行います。 `/log` および `/tmp` デプロイする前のディレクトリ。

- **ステージングにデプロイする前に、ローカル環境および統合環境でアップグレードを正常に計画して実行します。** — アップグレード後に、デプロイメントをテストし、問題を解決します。

- **コードをステージングに結合してから実稼動に結合する** — 変更を実稼動環境にプッシュする前に、ステージング環境で問題をテストして解決します。

- **アップグレード後のタスクを完了**-

   - SSH を使用してリモートサーバーにログインし、以下を確認します。

      - 必要に応じて、インデクサーのステータスと再インデックスを確認します。 詳しくは、 [インデクサーの管理](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/manage-indexers.html) （内） _設定ガイド_.

      - 次を確認します。 `cron` ログと `cron_schedule` 表をAdobe Commerceデータベースに表示して cron のステータスを確認し、必要に応じて cron ジョブを再実行します。
詳しくは、 [ログ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/configure-cron-jobs.html#logging) （内） _設定ガイド_.

   - アップグレード後のユーザー受け入れテスト UAT をステージング環境および実稼動環境で完了し、サードパーティおよびカスタム拡張機能のアップグレードに関連する問題を修正します。
