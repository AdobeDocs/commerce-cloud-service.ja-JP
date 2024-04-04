---
title: Fastly キャッシュをバイパスするカスタム VCL
description: Fastly キャッシュをバイパスするカスタム VCL スニペットを作成することで、オリジンサーバーへのリクエストトラフィックをトラブルシューティングします。
feature: Cloud, Configuration, Cache
exl-id: a2e9dc57-9b5e-4716-9965-a4324442ad00
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '301'
ht-degree: 0%

---

# Fastly キャッシュをバイパスするカスタム VCL

カスタム VCL スニペットを作成して Fastly キャッシュをバイパスし、オリジンサーバーへのリクエストトラフィックのトラブルシューティングをおこなうことができます。 例えば、サイトの問題がキャッシュによって発生したかどうかを判断するスニペットを作成したり、ヘッダーのトラブルシューティングをおこなったりできます。

特定の IP アドレスまたは URL からの要求に対して Fastly キャッシュをバイパスするようにスニペットを設定できます。

>[!NOTE]
>
>カスタム VCL 設定を実稼動環境に結合する前に、必ずステージング環境でコードをテストしてください。

**前提条件：**

{{$include /help/_includes/vcl-snippet-prerequisites.md}}

**IP アドレスまたは URL に基づいて Fastly キャッシュをバイパスするには**:

{{admin-login-step}}

1. クリック **ストア** /設定/ **設定** > **詳細** > **システム**.

1. 展開 **フルページキャッシュ** > **Fastly 設定** > **カスタム VCL スニペット**.

1. クリック **カスタムスニペットを作成**.

1. VCL スニペット値を追加します。

   - **名前** — `bypass_fastly`

   - **タイプ** — `recv`

   - **優先度** — `5`

   - **VCL** スニペットコンテンツ —

     次の例では、特定の IP アドレスに対して Fastly をバイパスしています。

     ```conf
     if (client.ip == "<Your IPv4 IP address>" || client.ip == "<Your IPv6 IP address>") {
       return(pass);
     }
     ```

     次の例では、特定の URL パターンに対して Fastly をバイパスしています。

     ```conf
     if (req.url ~ "/media/feeds/GoogleShoppingHiVisNew.xml") {  return (pass);}
     ```

     完全に一致する URL に対しては、 `==` 演算子（代わりに） `~` 演算子を使用します。 詳しくは、 [VCL リファレンスの失敗] 」を参照してください。

1. クリック **作成**.

   ![VCL スニペットをバイパスする Fastly を作成](/help/assets/cdn/fastly-create-bypass-snippet.png)

1. ページがリロードされたら、「 **VCL を Fastly にアップロード** （内） *Fastly 設定* 」セクションに入力します。

1. アップロードが完了したら、ページ上部の通知に従ってキャッシュを更新します。

   アップロードプロセス中に、更新された VCL バージョンを正しく検証します。 検証が失敗した場合は、カスタム VCL スニペットを編集して問題を修正します。 次に、VCL を再度アップロードします。

VCL スニペットを追加した後、cURL コマンドを使用して、次の例に示すように、指定した IP アドレスまたは URL からオリジンサーバーにリクエストを送信できます。

```bash
curl -svo /dev/null www.example.com/index.html
```

次に、応答を調べて、キャッシュされていないコンテンツの問題のトラブルシューティングをおこないます。

{{automate-vcl-snippet-deployment}}

{{$include /help/_includes/vcl-snippet-modify.md}}

{{$include /help/_includes/vcl-snippet-delete.md}}

<!--External link definitions-->

[VCL リファレンスの失敗]: https://docs.fastly.com/vcl/
