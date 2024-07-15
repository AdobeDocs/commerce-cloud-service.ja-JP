---
title: Variables プロパティ
description: variables プロパティを使用して、アプリケーションのストア設定オプション  [!DNL Commerce]  カスタマイズします。
feature: Cloud, Configuration
exl-id: 5cd92fbb-8bff-48b1-9658-500140591344
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '72'
ht-degree: 0%

---

# Variables プロパティ

アプリケーションベースの環境変数を使用して、ストア設定をカスタマイズできます。 これらの変数は、特定の構文を使用します。 [ 設定ガイド ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/paths/override-config-settings.html) の「_設定のオーバーライド_」を参照してください。

[!DNL Commerce] アプリケーションの特定のバージョンには、`.magento.app.yaml` ファイルに含まれる次の環境変数が必要です。

Adobe Commerce 2.2.x から 2.3.x へのアップグレードに必要：

```yaml
variables:
    env:
        CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
        CONFIG__STORES__DEFAULT__PAYMENT__BRAINTREE__CHANNEL: 'Magento_Enterprise_Cloud_BT'
        CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
```

Adobe Commerce 2.4.x の場合は、次の変数を設定します。

```yaml
variables:
    env:
        CONFIG__DEFAULT__PAYPAL_ONBOARDING__MIDDLEMAN_DOMAIN: 'payment-broker.magento.com'
        CONFIG__STORES__DEFAULT__PAYPAL__NOTATION_CODE: 'Magento_Enterprise_Cloud'
```
