---
title: プロジェクトのアップグレードのベストプラクティス
description: プロジェクト ファイルのアップグレードに関するベスト プラクティスの一覧を参照してください。
feature: Cloud, Best Practices, Upgrade
exl-id: 7d0a2627-e4c5-46b4-9e6c-24d20fa4f92f
source-git-commit: c61d711b1041ecf76ec6468cd225a34fd77c24b1
workflow-type: tm+mt
source-wordcount: '442'
ht-degree: 0%

---

# プロジェクトのアップグレードのベストプラクティス

ビルドおよびデプロイメントのベストプラクティスに従い、 [アップグレードとパッチ](../development/commerce-version.md) アプリケーションをアップグレードするワークフロー。 次のガイドラインに従って、アップグレードとアップグレード後の作業を計画します。

- **プロジェクトのバックアップ**- Adobe Commerceおよびサードパーティ製またはカスタムの拡張機能をアップグレードする前に、統合環境、ステージング環境および実稼動環境でデータベースをバックアップしてください。 参照： [データベースのバックアップ](../development/commerce-version.md#project-backup).

- **互換性の問題の確認**-

   - カスタムテーマが新しいAdobe Commerce バージョンと互換性があることを確認します

   - サードパーティおよびカスタム拡張機能をアップグレードした後は、 `magento-cloud local:build` デプロイする前に Composer の依存関係を検証するコマンド。

   - Adobe Commerceのリリースノートと拡張機能のドキュメントを参照して、アップグレードされたAdobe Commerce バージョンおよび拡張機能に関連する既知の機能問題やバグに対処するために必要な回避策や設定変更が実装されていることを確認します。

   - インストールされているサービスバージョンが新しいAdobe Commerce バージョンと互換性があることを確認し、必要に応じてサービスをアップグレードします。 参照： [サービス](../services/services-yaml.md).

   - データベースをテストして、Adobe Commerceのバージョンと拡張機能の更新で発生した問題に対処します。

   - リモート環境にデプロイする前に、環境固有の設定に対して必要な更新を行います。

   - 検索サービスのバージョンが、PHP クライアントのバージョンと互換性があることを確認します。 参照： [Elasticsearchの設定](../services/elasticsearch.md) または [OpenSearch の設定](../services/opensearch.md).

- **リモート環境でのデータベース接続と使用可能なストレージの確認**-

   - SSH を使用してリモートサーバーにログインし、MySQL データベースへの接続を確認します。 参照： [データベースへの接続](../services/mysql.md#connect-to-the-database).

   - リモート環境で使用可能なストレージを確認します。 `disk free` コマンドを使用して、クラウド環境で使用可能なディスク領域を表示および管理します。 参照： [ディスク容量の管理](../storage/manage-disk-space.md).

      - アップグレードしたデータベースのサイズを確認し、 `services.yaml` ファイルに十分なディスク領域が割り当てられています。

      - ディスク領域を解放する – キャッシュをクリアし、 `/log` および `/tmp` デプロイ前のディレクトリ。

- **ステージングにデプロイする前に、ローカル環境と統合環境でアップグレードを計画して正常に実行します**- アップグレード後、デプロイメントをテストし、問題を解決します。

- **コードをステージング環境に結合してから、実稼動環境に結合する** – 変更を実稼動環境にプッシュする前に、ステージング環境で問題をテストして解決します。

- **アップグレード後のタスクの完了**-

   - SSH を使用してリモートサーバーにログインし、以下を確認します。

      - インデクサーのステータスを確認し、必要に応じてインデックスを再作成します。 参照： [インデクサーの管理](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/manage-indexers.html) が含まれる _設定ガイド_.

      - を確認します `cron` ログと `cron_schedule` Adobe Commerce データベース内のテーブルを参照して cron ステータスを確認し、必要に応じて cron ジョブを再実行します。
参照： [ログ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/configure-cron-jobs.html#logging) が含まれる _設定ガイド_.

   - ステージング環境および実稼動環境でアップグレード後のユーザー受け入れテスト UAT を完了し、サードパーティおよびカスタム拡張機能のアップグレードに関連する問題を修正します。
