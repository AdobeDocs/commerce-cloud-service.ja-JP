---
title: ダウンタイムなしの導入
description: クラウドインフラストラクチャプロジェクトにAdobe Commerceをデプロイする際の、全体的なダウンタイムを短縮する方法を説明します。
feature: Cloud, Deploy, SCD, Themes
exl-id: ff89d2e1-dfc8-4f6d-bd98-947559af13f0
source-git-commit: 225fba1acfd8b3ce4d7ce989c7851e7b0b218680
workflow-type: tm+mt
source-wordcount: '448'
ht-degree: 0%

---

# ダウンタイムなしの導入

Adobe Commerce on cloud infrastructure は、 [_保守_ mode](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/setup/application-modes.html#production-mode) デプロイフェーズ中。デプロイメントが完了するまでサイトがオフラインになります。 実稼動サイトがメンテナンスモードになる時間の長さは、サイトのサイズ、デプロイメント中に適用される変更の数、静的コンテンツデプロイメントの設定によって異なります。 でデプロイされるようにプロジェクトを設定できます。 **ゼロ** ダウンタイム効果。

デプロイメントプロセス中、すべての接続は最大 5 分間キューに入れられ、アクティブなセッションや保留中のアクション（買い物かごへの追加やチェックアウトなど）は保持されます。 デプロイメント後、キューは解放され、接続は中断することなく続行されます。 これを使用するには _接続ホールド_ を使用して、 _ゼロ_ ダウンタイムが発生した場合は、最も効率的なデプロイ戦略を使用するようにプロジェクトを設定する必要があります。

次の手順を使用して、ストアが実稼動環境にアップデートをデプロイするのに要する時間を短縮します。

1. [へのアップグレード `ece-tools` パッケージ](../dev-tools/install-package.md) または [を更新します。 `ece-tools` version](../dev-tools/update-package.md)
Adobe Commerce on cloud infrastructure プロジェクトには、最新の `ece-tools` 最適なデプロイメントを設定するためのツールを使用できるように、パッケージ化します。 最新の `ece-tools`をクリックし、次の手順に進みます。

   >[!NOTE]
   >
   >最新の `ece-tools` パッケージの場合、ダウンタイムなしのデプロイメント方法は `ece-tools` [バージョン 2002.0.13](../release-notes/cloud-release-archive.md#v2002013) 後で。

1. [静的コンテンツデプロイメントの設定](static-content.md)
デプロイフェーズで静的コンテンツのデプロイメントが失敗した場合、サイトはメンテナンスモードで動作しなくなります。 ビルドフェーズ中に障害が発生した場合、デプロイフェーズは開始されないので、プロセスではダウンタイムを回避します。 [ビルドフェーズ中の静的コンテンツの生成 ( 縮小HTML)](static-content.md#setting-the-scd-on-build)は、理想的な状態とも呼ばれ、ダウンタイムなしのデプロイメントおよび _回避_ 障害が発生した場合のダウンタイム。

1. [デプロイ後のフックの設定](../application/hooks-property.md)
キャッシュをクリーンアップおよびウォーミングするには、デプロイ後のフックを設定する必要があります。 デフォルトでは、サイトがダウンした場合、デプロイフェーズでキャッシュのクリーンアップが発生します。 キャッシュをデプロイ後フェーズに移動すると、デプロイフェーズが完了するまでキャッシュが有効なままになり、キャッシュを安全にクリーンアップできます。

   キャッシュをプリロードする際に使用するページのリストをカスタマイズし、 [WARM_UP_PAGES 環境変数](../environment/variables-post-deploy.md#warmuppages).

1. [テーマファイルの縮小](../environment/variables-deploy.md#scdmatrix)
SCD\_MATRIX 環境変数を設定することで、不要なテーマ・ファイルの数を減らすことができます。

1. [静的コンテンツのデプロイメントの高速化](../environment/variables-deploy.md#scdthreads)
SCD\_THREADS環境変数を更新して、静的コンテンツデプロイメントのスレッド数を増やすことで、デプロイメントプロセスを高速化できます。

>[!NOTE]
>
>最適なデプロイメントを行うために、プロジェクト設定を検証するには、次の手順を実行します。 [理想的な状態ウィザードの実行](smart-wizards.md#verifying-an-ideal-configuration).
