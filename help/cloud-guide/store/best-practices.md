---
title: ストア設定のベストプラクティス
description: クラウドインフラストラクチャ上のAdobe Commerceでストアを設定する際のベストプラクティスについてお読みください。
feature: Cloud, Best Practices
exl-id: 01f528bd-74c2-42e7-8e77-7e6f57a40ef4
source-git-commit: 5b0a691a4355f5eda31d42cd3da9925439dfb510
workflow-type: tm+mt
source-wordcount: '1087'
ht-degree: 0%

---

# ストア設定のベストプラクティス

ストア、サイト、Web サイトの設定に関する詳細は、 [Adobe Commerce User Guide](https://experienceleague.adobe.com/docs/commerce-admin/user-guides/home.html). このページには、ストア、サイトなどの設定に関するベストプラクティス、役立つ情報、ガイドラインと、投稿するコンテンツが時間の経過と共に様々なバージョンにわたって提供されます。

## マーケティングキャンペーンとプロモーション

この情報は、クラウドインフラストラクチャ 2.1.X および 2.2.X 上のAdobe Commerceに役立ちます。

キャンペーンとプロモーションを作成するには、 [コンテンツのステージング](https://experienceleague.adobe.com/docs/commerce-admin/content-design/staging/content-staging.html). この機能を使用すると、キャンペーンを作成してプレビューし、顧客向けに公開できます。 次の情報は、役に立つ情報を提供します。 詳しい手順については、リンクされているAdobe Commerceユーザーガイドの内容を参照してください。

_キャンペーン_ は、季節ごとの販売や新しい製品ラインなどのマーケティングイベントです。 各キャンペーンには、カスタムテーマ、コンテンツのブロック、コンテンツを制御および表示するウィジェット、および価格ルールに関連するプロモーションを含めることができます。 キャンペーンは広範な性質を持つので、コンテンツステージングを通じて開始日と終了日を持つキャンペーンを作成します。

_プロモーション_ 割引、1 回限りのオファー、クーポン、初回の購入者のインセンティブなどを提供します。 これらのプロモーションは、 _価格ルール_ 顧客に購入を促す利用条件、割引、およびオプションを設定する 価格ルールは、 [買い物かご](https://experienceleague.adobe.com/docs/commerce-admin/marketing/promotions/cart-rules/price-rules-cart.html) または [カタログ](https://experienceleague.adobe.com/docs/commerce-admin/marketing/promotions/catalog-rules/price-rules-catalog.html)、バナー、報酬ポイントなどの追加のオプションを含む。 キャンペーンをプロモーション用にスケジュールし、新しい製品ラインや季節ごとの売上などの主要なイベントに価格ルールを適用できます。

以下は、プロモーションとキャンペーンの作成、更新、管理に役立つヒントです。

* プロモーションはキャンペーンの一部にすることができます。 キャンペーンをプロモーションに含めることはできません。 複数のキャンペーンで複数回使用するプロモーションのリストを価格ルールとして設定できます。
* プロモーションを作成すると、常に非アクティブな初期キャンペーンが作成されます。 開始日が設定されていますが、終了日は設定されていません。 この初期キャンペーンは無視できます。 正しいキャンペーンスケジュールで新しい更新をスケジュールし、アクティブにすることができます。
* キャンペーンには開始日と終了日があり、プロモーションには含まれません。 プロモーションの作成時に表示されるスケジューラーでは、プロモーションの開始日と終了日は設定されません。 プロモーションの設定ページにいる間、このプロモーション用のキャンペーンをスケジュールできます。
* ステージコンテンツでは直接編集できません。 キャンペーンの設定とオプションを編集する必要がある場合は、元のレプリカまたはレプリカを編集し、プッシュしてステージ済みコンテンツを上書きします。 例えば、キャンペーンの終了日が指定されていない場合、更新するには、元のキャンペーンとプッシュを編集する必要があります。

## 高度な価格設定とステージング済みコンテンツ

この情報は、クラウドインフラストラクチャ 2.1.X および 2.2.X 上のAdobe Commerceに役立ちます。

通常、 [高度な価格](https://experienceleague.adobe.com/docs/commerce-admin/catalog/products/pricing/pricing-advanced.html) を通じて製品を **製品** > **カタログ** 管理者の領域 ステージング済みコンテンツを使用して、プロモーションとキャンペーンに価格を追加するための追加の手順を実行します。

「Advanced Pricing」を編集し、「Content Staging」を更新する手順は、次のとおりです。

1. 管理者にログインします。
1. に移動します。 **製品** > **カタログ** 製品を選択し、編集します。
1. 「価格」タブで、「 **高度な価格**. 価格を編集し、変更を保存します。
1. ページ上部で、「 **新しい更新をスケジュール**.
1. 製品のプロモーションを作成します。
1. プロモーション情報を入力します。 「スケジューラー」に、開始日時と終了日時を入力します。
1. プロモーションを保存します。 非アクティブな初期キャンペーンが作成されます。
1. プレビューを使用して、キャンペーンの特別な価格、プロモーション名、通常の価格、スケジュールされた日付範囲を確認できます。

その他の手順については、 [カタログ価格ルールのスケジュール変更](https://experienceleague.adobe.com/docs/commerce-admin/marketing/promotions/catalog-rules/price-rule-catalog-scheduled-changes.html). クリック **次へ** 階段を歩いて行く

## 価格ルール

価格ルールには、マーケティングの想像力と同様に、限りのないロジックと条件を含めることができます。 一般的な例としては、Buy One Get One Free、Buy One Get One 50% Off、$100 ドル以上の注文で$25 ドルオフなどがあります。

価格ルールを作成する手順は、「 [Adobe Commerce User Guide](https://experienceleague.adobe.com/docs/commerce-admin/marketing/promotions/catalog-rules/price-rules-catalog-create.html).

次の例では、最初の注文のみの割引の価格ルールを作成します。 この割引の場合は、次の操作を行います。

* 価格ルールの作成 [顧客セグメント](https://docs.magento.com/user-guide/marketing/customer-segment-price-rule.html) 条件付き：合計注文数が 1 未満
* この顧客セグメントを買い物かごルールに条件として追加
* オプション — 特定の SKU または製品のカテゴリに割引を適用し、購入を重点的におこなうための条件とルールを追加します。

これにより、新規の顧客や、購入していない既存の顧客は、最初の注文時にのみ割引を受け取ります。 バナーを作成し、初回購入割引の電子メールプロモーションを送信できます。

## ストア表示

クラウドインフラストラクチャ上にAdobe Commerceを 1 回実装して、複数のストアを設定して実行できます。 詳しくは、 [複数の Web サイトまたはストアを設定する](multiple-sites.md).

互いにやり取りしないストアの場合は、 _web サイト_. 各 Web サイトには、Adobe Commerceの他の Web サイトと共有されない特定の記事、顧客データ、チェックアウト、買い物かごがあります。

各 Web サイトには、1 つ以上の _ストア_ 様々なカテゴリや記事、共有された顧客データ、チェックアウト、買い物かご。 これらの店舗では、顧客は一度サインアップし、1 回のチェックアウトで異なる商品カタログをまたいで買い物をすることができます。

また、 _ストア表示_ 様々な言語、レイアウト、デザインの場合。 各ビューには、記事、顧客データ、チェックアウト、買い物かごを共有する際に、個別のドメイン、ブランディング、言語を設定できます。

次に、説明をより適切にする例を示します。

* 英語とスペイン語のロケール用に 1 つのストアと 2 つのビューを持つ単一の Web サイト。 記事のデータ、顧客、チェックアウト、買い物かごはすべて共有されます。

  ![ストアの例 1](../../assets/example-store1.png)

* 女性服店付きの単一のウェブサイトには、英語とスペイン語の 2 つのビューがあります。 子ども服の店内には、英語での単一店舗ビューがあります。 記事のデータ、顧客、チェックアウト、買い物かごはすべて共有されます。 ストアには、異なるドメインとテーマが含まれている場合があります。

  ![ストアの例 2](../../assets/example-store2.png)

* 異なるカタログと別々の記事、顧客データ、買い物かごを持つ家庭用の服用と買い物かご用の 2 つの Web サイト。 各 Web サイトには、記事、顧客データ、チェックアウト、買い物かごをその Web サイト内でのみ共有する複数の店舗や表示を持つことができます。

  ![ストアの例 3](../../assets/example-store3.png)

>[!WARNING]
>
>Web サイトや店舗の数が増えると、カタログデータが展開します。 プロジェクトのアーキテクチャによっては、追加のストアを使用すると、インデックス作成プロセスが長くなり、キャッシュされていないカタログページの応答時間が長くなる場合があります。 Adobeでは、サイトのパフォーマンスを詳細に監視することをお勧めします。