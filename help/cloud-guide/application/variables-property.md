---
title: Variables プロパティ
description: variables プロパティを使用して、 [!DNL Commerce] アプリケーション。
feature: Cloud, Configuration
exl-id: 5cd92fbb-8bff-48b1-9658-500140591344
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '72'
ht-degree: 0%

---

# Variables プロパティ

アプリケーションベースの環境変数を使用して、ストア設定をカスタマイズできます。 これらの変数は、特定の構文を使用します。 詳しくは、 [設定の上書き](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/paths/override-config-settings.html) （内） _設定ガイド_.

次の環境変数が `.magento.app.yaml` ファイルは、 [!DNL Commerce] アプリケーション。

Adobe Commerce 2.2.x から 2.3.x に必要：

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
