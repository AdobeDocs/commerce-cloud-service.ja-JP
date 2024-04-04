---
title: アクティビティストリーム
description: のアクティビティストリームを読み取る方法を説明します。 [!DNL Cloud Console] または Cloud 上のAdobe Commerceインフラストラクチャ用の Cloud CLI
last-substantial-update: 2024-02-06T00:00:00Z
exl-id: ffef5ab4-ef40-4073-adc8-a44c61c0d77b
source-git-commit: 85ff1283f773823ff2c6e6ab8f391fd5b4aa00e4
workflow-type: tm+mt
source-wordcount: '449'
ht-degree: 0%

---

# アクティビティストリーム

各環境のメインビューには、 **アクティビティ** Git ログに似た過去のイベントのリストです。 アクティビティリストは、アクティブな環境の最近のイベントのストリームです。 次に、アクティビティストリームに表示されるアクティビティのタイプとアイコンの一覧を示します。

![アクティビティのタイプ](../../assets/activity-types.svg){width="500" align="center"}

## ログを表示

「アクティビティ」リストで、アクティビティのステータスアイコンをクリックしてログを表示します。 または、 ![その他](../../assets/icon-more.png){width="32"} (_その他_) メニューを使用して、アクティビティ管理のその他のオプションにアクセスできます。 次に、バックアップを作成する短いログを示します。 以下が可能です。 [Cloud CLI を使用する](#activity-stream-with-cloud-cli) 同じログを表示します。

![ログ表示](../../assets/log-view.png)

## アクティビティの管理

一部のアクティビティは、 _実行中_ または _保留中_ ステータス。 実行中のデプロイメントのキャンセルなど、実行中のアクティビティに対して操作を実行できます。 次のタブに、アクティビティをキャンセルする 2 つの方法を示します。 [!DNL Cloud Console] または Cloud CLI。

>[!BEGINTABS]

>[!TAB コンソール]

**アクティビティをキャンセルするには、[!DNL Cloud Console]**:

実行中のアクティビティに対してアクションを実行するには、 ![その他](../../assets/icon-more.png){width="32"} (_その他_) メニューを開き、次のようなアクションを選択します。 `Cancel` または `View log`. この例では、 **キャンセル** オプションを使用して、実行中のアクティビティを停止します。

すべてのアクティビティにキャンセルオプションがあるわけではありません。 例えば、アプリケーションのデプロイメントをキャンセルするオプションは、 _ビルド_ フェーズ。 アプリケーションが _デプロイ_ フェーズでは、アクティビティをキャンセルできなくなりました。 詳しくは、 [デプロイメントプロセス](../deploy/process.md) 様々な段階について

![アクティビティをキャンセル](../../assets/activity-icons/cancel-activity.png){width="450" align="center"}

ターミナルでデプロイメントアクティビティを実行している場合は、 [!DNL Cloud Console] その結果、ターミナルでキャンセルが発生します。

![ターミナルでキャンセルされたアクティビティ](../../assets/activity-icons/activity-cancelled.png){width="300"}

>[!TAB CLI]

**Cloud CLI でアクティビティをキャンセルするには、以下を実行します。**:

1. 実行中のアクティビティを特定し、アクティビティ ID を選択します。

   ```bash
   magento-cloud activity:list --state=in_progress
   ```

1. アクティビティ ID を使用してアクティビティをキャンセルします。

   ```bash
   magento-cloud activity:cancel wvl5wm7s5vkhy
   ```

>[!ENDTABS]

## アクティビティストリームをフィルタリング

アクティビティリストをフィルタリングする機能は、バックアップや結合イベントなど、特定のものを探す場合に役立ちます。

**アクティビティリストをフィルターするには、次の手順に従います。[!DNL Cloud Console]**:

1. 環境を選択し、アクティビティを選択します。 **[!UICONTROL All]** イベント履歴全体を含めるビュー。

1. クリック ![フィルター条件](../../assets/icon-filterby.png){width="32"} をクリックし、 **[!UICONTROL Filter by]** options:

   ![アクティビティをフィルター](../../assets/activity-filter.png)

1. アクティビティを選択 **[!UICONTROL Recent]** リストを表示し、リセットします。

## Cloud CLI でストリームを表示

The `magento-cloud` CLI は、 [!DNL Cloud Console]. The `activity` コマンドを使用すると、次のことができます。

- `list` 環境のアクティビティの流れ
- `get` 特定のアクティビティの詳細
- を表示します。 `log` 特定のアクティビティの
- `cancel` 活動

**Cloud CLI を使用してアクティビティストリームを表示するには**:

1. 現在の環境のアクティビティのリストを表示します。

   ```bash
   magento-cloud activity:list
   ```

1. 各アクティビティには一意の ID が割り当てられます。 前のリストから ID を選択し、そのアクティビティの詳細を表示します。

   ```bash
   magento-cloud activity:get wvl5wm7s5vkhy
   ```

1. そのアクティビティの完全なログを表示します。

   ```bash
   magento-cloud activity:log wvl5wm7s5vkhy
   ```

   レスポンスのサンプル：

   ```bash
   Activity ID: wvl5wm7s5vkhy
   Type: environment.backup
   Description: User created a backup of Master
   Created: 2023-09-08T14:03:33+00:00
   State: complete
   Log:
   Creating backup of master
   Created backup eg5pu63egt2dcojkljalzjdopa
   ```
