---
source-git-commit: 8f1ed3067f6daed897151052c8b9f987d3df3a50
workflow-type: tm+mt
source-wordcount: '93'
ht-degree: 0%

---
# Fastly のカスタム VCL を削除するファイルを含めます

## カスタム VCL スニペットを削除します。

1. [ログイン](/help/get-started/onboarding.md#access-your-admin-panel) 管理者に問い合わせます。

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

   ![カスタム VCL スニペットの管理](/help/assets/cdn/fastly-manage-snippets.png)

1. Adobe Analytics の _アクション_ 列で、削除するスニペットの横にあるごみ箱アイコンをクリックします。

1. 次のモーダルウィンドウで、「 **DELETE** 新しいバージョンをアクティベートします。

>[!WARNING]
>
>The _カスタム VCL スニペット_ 「 UI 」オプションには、 Adobe Commerce Admin で追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して [管理者](/help/cloud-guide/cdn/fastly-vcl-custom-snippets.md#manage-vcl-using-the-api).
