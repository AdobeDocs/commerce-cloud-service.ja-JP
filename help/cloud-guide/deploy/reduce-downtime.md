---
title: ダウンタイムなしのデプロイメント
description: クラウドインフラストラクチャプロジェクトにAdobe Commerceをデプロイする際の全体的なダウンタイムを短縮する方法について説明します。
feature: Cloud, Deploy, SCD, Themes
exl-id: ff89d2e1-dfc8-4f6d-bd98-947559af13f0
source-git-commit: 225fba1acfd8b3ce4d7ce989c7851e7b0b218680
workflow-type: tm+mt
source-wordcount: '448'
ht-degree: 0%

---

# ダウンタイムなしのデプロイメント

クラウドインフラストラクチャー上のAdobe Commerceは、でアプリケーションを実行します。 [_整備_ モード](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/setup/application-modes.html#production-mode) デプロイフェーズでは、デプロイメントが完了するまでサイトがオフラインになります。 実稼動サイトがメンテナンスモードになっている時間の長さは、サイトのサイズ、デプロイメント中に適用される変更の数、静的コンテンツのデプロイメントの設定によって異なります。 でデプロイされるようにプロジェクトを設定することができます。 **ゼロ** ダウンタイム効果。

デプロイメントプロセス中、すべての接続は最大 5 分間キューに入り、アクティブなセッションと保留中のアクション（買い物かごへの追加やチェックアウトなど）が保持されます。 デプロイ後、キューは解放され、接続は中断されずに続行されます。 これを使用するには _接続保留_ お客様に有利に働き、 _ゼロ_ ダウンタイムが発生すると、最も効率的なデプロイ戦略を使用するようにプロジェクトを設定する必要があります。

次の手順を使用して、ストアが更新プログラムを実稼動環境にデプロイするまでにかかる時間を短縮します。

1. [へのアップグレード `ece-tools` package](../dev-tools/install-package.md) または [を更新 `ece-tools` version](../dev-tools/update-package.md)
クラウドインフラストラクチャー上のAdobe Commerce プロジェクトには、最新のものが必要です `ece-tools` パッケージ化して、最適なデプロイメントを設定するためのツールを使用できるようにします。 最新の `ece-tools`次の手順に進みます。

   >[!NOTE]
   >
   >最新のを使用することがベストプラクティスであるにもかかわらず `ece-tools` ダウンタイムなしのデプロイメント方法を使用したパッケージは、 `ece-tools` [バージョン 2002.0.13](../release-notes/cloud-release-archive.md#v2002013) およびあと。

1. [静的コンテンツデプロイメントの設定](static-content.md)
デプロイフェーズで静的コンテンツのデプロイメントが失敗すると、サイトがメンテナンスモードで停止します。 ビルドフェーズでエラーが発生した場合、プロセスはデプロイフェーズを開始しないので、ダウンタイムを回避できます。 [縮小されたHTMLを使用した、ビルドフェーズでの静的コンテンツの生成](static-content.md#setting-the-scd-on-build)理想的な状態とも呼ばれ、ダウンタイムのないデプロイメントに最適な設定で、 _妨げる_ エラー発生時のダウンタイム。

1. [デプロイ後フックの設定](../application/hooks-property.md)
キャッシュをクリーンアップおよびウォームするには、デプロイ後のフックを設定する必要があります。 デフォルトでは、サイトがダウンした場合、デプロイフェーズでキャッシュクリーンアップが実行されます。 デプロイ後のフェーズにキャッシュのクリーンアップを移動すると、デプロイフェーズが完了するまでキャッシュがライブのままになり、キャッシュを安全にクリーンアップできます。

   を使用した、キャッシュのプリロードに使用するページのリストのカスタマイズ [WARM_UP_PAGES 環境変数](../environment/variables-post-deploy.md#warmuppages).

1. [テーマファイルを削減](../environment/variables-deploy.md#scdmatrix)
SCD\_MATRIX 環境変数を設定することで、不要なテーマファイルの数を減らすことができます。

1. [静的コンテンツのデプロイメントを迅速化](../environment/variables-deploy.md#scdthreads)
SCD\_THREADS環境変数を更新して、静的コンテンツのデプロイメントのスレッド数を増やすと、デプロイメントプロセスを高速化できます。

>[!NOTE]
>
>最適なデプロイメントを得るために、次の方法でプロジェクト設定を検証できます。 [理想的な状態ウィザードの実行](smart-wizards.md#verifying-an-ideal-configuration).
