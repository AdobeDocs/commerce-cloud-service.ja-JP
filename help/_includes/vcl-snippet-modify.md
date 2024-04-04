---
source-git-commit: 2d902a3926c6bbc6a9dc8afcbd667eddeaf3be7e
workflow-type: tm+mt
source-wordcount: '112'
ht-degree: 0%

---
# Fastly のカスタム VCL を修正するためのファイルを含めます

## カスタム VCL スニペットを変更する

1. [ログイン](/help/get-started/onboarding.md#access-your-admin-panel) 管理者に問い合わせます。

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

   ![カスタム VCL スニペットの管理](/help/assets/cdn/fastly-manage-snippets.png)

1. Adobe Analytics の _アクション_ 列で、編集するスニペットの横にある設定アイコンをクリックします。

1. ページがリロードされたら、「 **VCL を Fastly にアップロード** （内） _Fastly 設定_ 」セクションに入力します。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

>[!WARNING]
>
>The _カスタム VCL スニペット_ 「 UI 」オプションには、 Adobe Commerce Admin で追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して [管理者](/help/cloud-guide/cdn/fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api).
