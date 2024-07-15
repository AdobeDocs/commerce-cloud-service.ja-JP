---
source-git-commit: 2d902a3926c6bbc6a9dc8afcbd667eddeaf3be7e
workflow-type: tm+mt
source-wordcount: '112'
ht-degree: 0%

---
# Fastly のカスタム VCL を変更するためのファイルを含める

## カスタム VCL スニペットの変更

1. 管理者に [ ログイン ](/help/get-started/onboarding.md#access-your-admin-panel) します。

1. **ストア**/**設定**/**設定**/**詳細**/**システム** をクリックします。

1. **フルページキャッシュ**/**Fastly 設定**/**カスタム VCL スニペット** の順に展開します。

   ![ カスタム VCL スニペットの管理 ](/help/assets/cdn/fastly-manage-snippets.png)

1. _アクション_ 列で、編集するスニペットの横にある設定アイコンをクリックします。

1. ページのリロード後、「**Fastly 設定 _セクションの**Fastly に VCL をアップロード_ をクリックします。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

>[!WARNING]
>
>「_カスタム VCL スニペット_ UI オプションには、Adobe Commerce管理を通じて追加されたスニペットのみが表示されます。 Fastly API を使用してスニペットを追加する場合は、API を使用して [ スニペットを管理 ](/help/cloud-guide/cdn/fastly-vcl-custom-snippets.md#manage-custom-vcl-snippets-using-the-api) します。
