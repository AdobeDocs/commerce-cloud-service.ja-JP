---
title: Commerceの管理パネルへのアクセス
description: Commerce管理パネルへのアクセス方法について説明します。
recommendations: noDisplay, catalog
exl-id: 9a8a0a49-b108-48bd-b413-ec9431370c06
source-git-commit: 3ca09243dc0a714c1d86cccf9f0620a8a39fd1e1
workflow-type: tm+mt
source-wordcount: '323'
ht-degree: 0%

---

# Commerceの管理パネルへのアクセス

Commerce管理パネルへの管理アクセス権を持つユーザーは、ユーザーの追加、ストアサービスの設定、ストアの設定とカスタマイズ作業の完了などを行うことができます。

新規プロジェクトの場合、「ようこそ」のメールを受信した後の最初の手順は、ライセンス所有者アカウントのパスワードを変更して、プロジェクトへの管理者アクセスを保護することです。 このアカウントのデフォルトのユーザー名は、ライセンス所有者のメールアドレスです。

次のいずれかの方法を使用して、パスワード変更リクエストを送信できます。

- ライセンス所有者のメールアドレスに送信されたようこそメールを探し、リンクに従ってパスワードを変更します。

- からストア URL をコピー [[!DNL Cloud Console]](../cloud-guide/project/overview.md) ブラウザーに移動します。 次に、を追加します `/admin` URL の末尾に移動して、サインインページを開きます。 「」をクリックします **パスワードをお忘れですか？** パスワード変更要求をライセンス所有者の電子メール アドレスに送信するためのリンクです。

パスワード変更リクエストを送信したら、パスワードのリセット通知が届いていないかどうかをメールで確認します。 メールが届かない場合は、スパムフォルダーを確認してください。

>[!TIP]
>
>パスワードのリセットに失敗した場合や管理パネルにログインできない場合は、管理者アクセス権を持つユーザーが SSH を使用してプロジェクトに接続し、を使用して管理者ユーザーを追加できます。 `admin:user:create` CLI コマンド。 参照： [管理者アカウントを作成、編集またはロック解除](https://experienceleague.adobe.com/docs/commerce-operations/installation-guide/tutorials/admin.html) が含まれる _インストールガイド_.

## サイトの正常性の監視

この [サイト全体分析ツール](https://experienceleague.adobe.com/en/docs/commerce-operations/tools/site-wide-analysis-tool/intro) は、プロアクティブなセルフサービスツールで、Adobe Commerce インストールのセキュリティと操作性を確保するための詳細なシステムインサイトおよびレコメンデーションが含まれている中央リポジトリです。 24 時間 365 日、パフォーマンスの監視、レポート、アドバイスをリアルタイムで行うことで、潜在的な問題を特定し、サイトの正常性、安全性、アプリケーションの設定をより明確に把握します。 これにより、解決時間が短縮され、サイトの安定性とパフォーマンスが向上します。 からサイト全体の分析ツールに直接アクセスできます。 [管理パネル](https://experienceleague.adobe.com/en/docs/commerce-operations/tools/site-wide-analysis-tool/access#option-2-logging-in-to-your-site-wide-analysis-tool-dashboard-from-your-stores-admin-panel) または [専用ドメイン](https://experienceleague.adobe.com/en/docs/commerce-operations/tools/site-wide-analysis-tool/access#option-1-logging-in-to-your-site-wide-analysis-tool-dashboard-directly-from-the-site-wide-analysis-tool-domain-for-adobe-commerce-on-cloud-infrastructure-only) （クラウドインフラストラクチャプロジェクトでのAdobe Commerceのみ）。
