---
source-git-commit: 2d902a3926c6bbc6a9dc8afcbd667eddeaf3be7e
workflow-type: tm+mt
source-wordcount: '112'
ht-degree: 0%

---
# Fastly のカスタム VCL を変更するためのファイルを含める

## カスタム VCL スニペットの変更

1. [ログイン](/help/get-started/onboarding.md#access-your-admin-panel) を管理者に送信します。

1. クリック **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. を展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

   ![カスタム VCL スニペットの管理](/help/assets/cdn/fastly-manage-snippets.png)

1. が含まれる _アクション_ 列で、編集するスニペットの横にある「設定」アイコンをクリックします。

1. ページの再読み込み後、 **Fastly への VCL のアップロード** が含まれる _Fastly 設定_ セクション。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

>[!WARNING]
>
>この _カスタム VCL スニペット_ UI オプションには、Adobe Commerce管理者を通じて追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して以下を行います [それらを管理](/help/cloud-guide/cdn/fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api).
