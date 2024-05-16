---
title: Cloud でのCommerceのプロビジョニング
description: クラウドインフラストラクチャプロジェクトでAdobe Commerceをプロビジョニングするための、Adobeのカスタマーテクニカルアドバイザーの準備方法について説明します。
recommendations: noDisplay, catalog
role: Admin
exl-id: cfb354b0-c255-4b6e-94aa-c5a6bf7230d6
source-git-commit: 89c57a486545d6165e0407f913f4fa4cf6c95abe
workflow-type: tm+mt
source-wordcount: '728'
ht-degree: 0%

---

# Commerce on Cloud プロビジョニングの前提条件

それでは、クラウドインフラストラクチャー上でCommerce プロジェクトを開始して初期化しましょう。

Adobeがクラウドプロジェクト環境にCommerceをプロビジョニングする前に、次の方策を検討し、最初にAdobeアカウントチームに問い合わせるための回答を準備することをお勧めします。 次の節をチェックリストとして使用して、お客様のテクニカルアドバイザーとクラウドプロジェクトをプロビジョニングするための会話の準備に役立ててください。

## ドメイン定義

**Question 1**: _サイトの起動にどのドメインを使用しますか？_

ステージング環境および実稼動環境用の最上位ドメインとサブドメインを定義して、Fastly サービスと Nginx サービスの統合を準備します。 初期設定後は、Adobe Commerce サポートチケットを送信することによってのみドメインを追加できるので、ドメイン情報の準備を整えることをお勧めします。

実稼働ドメインとステージングドメインの例：

- `www.your-store.com`
- `your-store.com`
- `mcprod.your-store.com`
- `mcstaging.your-store.com`

参照： [複数の web サイトまたはストアを設定](../cloud-guide/store/multiple-sites.md) が含まれる _クラウドインフラストラクチャー上の Commerce_ 複数または一意のドメインに関する詳細なガイダンスのガイド。

## トランザクションメールドメイン

**質問 2**: _トランザクションメールにどのドメインを使用する予定ですか？_

クラウド上のAdobe Commerceでは、送信メール認証サービスと評価モニタリングサービスを提供する SendGrid Simple Mail Transfer Protocol （SMTP）プロキシサービスを使用しています。 SendGrid は、トランザクションメールをユーザーに代わって送信するので、ドメイン情報が必要です。

SendGrid ドメインの例： `example@your-store.com`

参照： [SendGrid メールサービス](../cloud-guide/project/sendgrid.md) が含まれる _クラウドインフラストラクチャー上の Commerce_ トランザクションメールとドメイン設定に関する詳細なガイダンスのガイド。

## ストレージ割り当て

**質問 3**: _ファイルのアップロードとデータベースに割り当てる契約済みストレージの容量はどれくらいですか。_

クラウドインフラストラクチャー上のAdobe Commerceでは、ファイル構造、検索インデックス作成、データベースにストレージを使用します。 必要に応じて、パーティションごとに 10 GB から始めてストレージを細分できます。

クラウドプロジェクト用に契約したストレージ量は、各環境の合計ストレージ量を表します。 例えば、50 GB のストレージスペースを購入した場合、各環境に 50 GB のストレージがあります。 実稼動環境、ステージング環境、各統合環境用にそれぞれ 50 GB の個別のストレージがあります。

契約済みストレージはいつでも増やすことができます。 実稼動環境とステージング環境の場合は、Adobe Commerce サポートチケットを送信して、ディスク容量の割り当てを変更する必要があります。 実稼動環境とステージング環境のサイズの増加は、特定の間隔でのみ発生します。 現在のディスク容量の使用状況に応じて、サポートチームはディスク容量の割り当てを最小 10 GB 増やすことをお勧めすることがあります。 割り当てが完了すると、ステージング環境および実稼動環境のストレージが増えます **ではない** 元に戻す。

参照： [ディスク容量の管理](../cloud-guide/storage/manage-disk-space.md) が含まれる _クラウドインフラストラクチャー上の Commerce_ ガイド。

## クラウドサービス地域

**質問 4**: _近接するのに最も便利なクラウドサービス地域はどれですか？_

Adobe Commerce on cloud infrastructure Pro プロジェクトの Infrastructure as a Service （IaaS）基盤として、Amazon Web Services（AWS）またはMicrosoft Azure のいずれかを選択します。 各サービスプロバイダーは複数の地域で動作し、複数のアベイラビリティーゾーンを提供します。 場所に適した地域を選択し、待ち時間やコストの増加の可能性を減らします。

参照： [Adobe Commerceクラウド地域地図](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/infrastructure/cloud/regions.html) が含まれる _実装プレイブック_.

## 接続サービス

**質問 5**: _PrivateLink サービスが必要ですか？ その場合、PrivateLink 接続はどの地域にありますか。_

クラウドインフラストラクチャー上のAdobe Commerceは、AWS PrivateLink または Azure Private Link サービスとの統合をサポートします。 このサービスはオプションですが、PrivateLink は、クラウドインフラストラクチャ環境間で、外部システムでホストされるサービスやアプリケーションとの安全なプライベート通信を確立するために使用されます。

Adobe Commerce インスタンスが同じリージョン内でアクセスできるように、クラウドサービスの戦略（AWSまたは Azure）を検討することが重要です。 参照： [PrivateLink サービス](../cloud-guide/development/privatelink-service.md) が含まれる _クラウドインフラストラクチャー上の Commerce_ 地域のアクセシビリティに関する詳細な説明のためのガイド。

## ターゲットサイトの起動

**質問 6**: _目標ローンチ日は何ですか？_

サイトを起動するには、サイトの起動を確実に成功させるために、反復的な設定とテストが必要です。 目標日を設定すると、ユーザーとAdobeアカウントチームが、最終的なローンチ前のアクティビティ（最終手順を調整するための呼び出しを含む）の準備を整えるのに役立ちます。

を参照してください。 [ローンチサイトの概要](../cloud-guide/launch/overview.md) が含まれる _クラウドインフラストラクチャー上の Commerce_ プロセス全体を確認し、起動チェックリストのコピーをダウンロードするためのガイドです。

>[!TIP]
>
> クラウドポータルを素早く確認し、新しいクラウドプロジェクトにアクセスします。
>
>**次の手順**: [Commerceのオンボーディング](onboarding.md)
