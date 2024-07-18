---
title: SendGrid メールサービス
description: クラウドインフラストラクチャ上のAdobe Commerce用 SendGrid メールサービスと、DNS 設定をテストする方法について説明します。
exl-id: 30d3c780-603d-4cde-ab65-44f73c04f34d
source-git-commit: b49a51aba56f79b5253eeacb1adf473f42bb8959
workflow-type: tm+mt
source-wordcount: '1090'
ht-degree: 0%

---

# SendGrid メールサービス

SendGrid Simple Mail Transfer Protocol （SMTP） プロキシサービスは、以下のサポートを含む、送信メール認証および評判の監視サービスを提供します。

* すべての送信トランザクションメール
* 専用 IP アドレス
* メールドメイン検証用のドメイン登録、DomainKeys Identified Mail （DKIM）署名（ステージング環境および実稼動環境のみ）
* カスタムドメイン登録（Pro のみ）
* Starter と Pro の統合環境向けの自動統合。 実稼動環境とステージング環境の場合は、Infrastructure as a Service （IaaS）のハードウェアプロビジョニングプロセス中に、手動によるプロビジョニングと設定が必要です

SendGrid SMTP プロキシは、受信メールを受信するための汎用的なメールサーバーとして使用したり、メールマーケティングキャンペーンで使用したりすることを目的としていません。

>[!TIP]
>
>[ オンボーディング UI](https://cloud.magento.com) でアカウントの SendGrid の詳細を確認し、**プロジェクトの詳細**/**ホスティング情報** タブを選択します。

## メールを有効または無効にする

Cloud Console またはコマンドラインから、各環境の送信メールを有効または無効にすることができます。

デフォルトでは、実稼動環境およびステージング環境では、送信メールが有効になっています。 ただし、[ コマンドライン ](outgoing-emails.md#enable-emails-in-the-cli)[!UICONTROL Outgoing emails] たは [Cloud Console](outgoing-emails.md#enable-emails-in-the-cloud-console) を使用して `enable_smtp` プロパティを設定するまで、環境設定で無効と表示される場合があります。 統合およびステージング環境で送信メールを有効にして、クラウドプロジェクトのユーザーに対して二要素認証またはパスワードリセットのメールを送信できます。 [ テスト用メールの設定 ](outgoing-emails.md) を参照してください。

実稼動環境またはステージング環境で送信メールを無効にするか再度有効にする必要がある場合は、[Adobe Commerce サポートチケット ](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide) を送信できます。

>[!TIP]
>
>[ コマンドライン ](outgoing-emails.md#enable-emails-in-the-cli) で [!UICONTROL enable_smtp] プロパティ値を更新すると、[Cloud Console](outgoing-emails.md#enable-emails-in-the-cloud-console) でもこの環境の [!UICONTROL Enable outgoing emails] 設定値が変更されます。

## SendGrid ダッシュボード

すべてのクラウドプロジェクトは中央アカウントで管理されるので、SendGrid ダッシュボードにアクセスできるのはサポートのみです。 SendGrid は、サブアカウント制限機能を提供していません。

アクティビティログで配信ステータスや、バウンスメールアドレス、却下メールアドレス、ブロックされたメールアドレスのリストを確認するには、[Adobe Commerce サポートチケットを送信 ](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket) します。 サポートチームは、30 日を経過したアクティビティログを取得 **できません**。

可能であれば、次の情報をリクエストに含めます。

* 影響を受ける 1 つまたは複数のメールアドレス
* 対象の期間（過去 30 日以内のみ）
* 電子メールの件名

## DomainKeys Identified Mail （DKIM）

DKIM は、インターネットサービスプロバイダー（ISP）が正当な送信者アドレスと偽物の送信者アドレスの両方を識別できるようにするメール認証技術です。これは、フィッシングやメール詐欺で一般的に使用される技術です。 DKIM は、DNS レコードを管理するドメイン所有者に依存しています。 DKIM を使用する場合、送信者サーバーは秘密鍵を使用してメッセージに署名します。 また、ドメイン所有者は、変更された `TXT` レコードである DKIM レコードを送信者ドメインの DNS レコードに追加します。 この `TXT` レコードには、受信者のメールサーバーがメッセージの署名を検証するために使用する公開鍵が含まれています。 DKIM 公開鍵暗号手順を使用すると、受信者は送信者の信頼性を検証できます。 [DKIM レコードの説明 ](https://docs.sendgrid.com/ui/account-and-settings/dkim-records) を参照してください。

>[!WARNING]
>
>SendGrid DKIM 署名とドメイン認証のサポートは、スタータープロジェクトではなく、Pro プロジェクトでのみ使用できます。 その結果、送信トランザクションメールはスパムフィルターによってフラグ付けされる可能性が高くなります。 DKIM を使用すると、認証済みメール送信者としての配信率が向上します。 メッセージ配信率を向上させるには、Starter から Pro にアップグレードするか、独自の SMTP サーバーまたはメール配信サービスプロバイダーを使用します。 [ 管理システムガイド ](https://experienceleague.adobe.com/en/docs/commerce-admin/systems/communications/email-communications) の「_メール接続の設定_」を参照してください。

### 送信者とドメインの認証

SendGrid が実稼動環境またはステージング環境からユーザーに代わってトランザクションメールを送信するには、3 つの SendGrid サブドメイン DNS エントリを含めるように DNS 設定を設定する必要があります。 各 SendGrid アカウントには、送信メールの認証に使用される一意の `TXT` レコードが割り当てられます。

**ドメイン認証を有効にするには**:

1. [ サポートチケット ](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket) を送信して、特定のドメインに対する DKIM の有効化をリクエストします（**Pro ステージング環境と実稼動環境のみ**）。
1. サポートチケットで提供された `TXT` および `CNAME` レコードを使用して、DNS 設定を更新します。

**アカウント ID を持つ `TXT` レコードの例**:

```text
v=spf1 include:u17504801.wl.sendgrid.net -all
```

**レコード `CNAME` 例**:

| ドメイン | 参照先 | レコードタイプ |
| ---------- | ---------- | ------------- |
| em.emaildomain.com | uxxxxxx.wl.sendgrid.net | CNAME |
| s1。_domainkey.emaildomain.com | s1.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |
| s2。_domainkey.emaildomain.com | s2.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |

### DKIM 署名と自動セキュリティ

認証済みドメインを設定する際に、自動セキュリティと手動セキュリティのどちらかを選択できます。 自動セキュリティを選択した場合、SendGrid は DKIM と SPF レコードを自動的に管理します。 新しい送信専用 IP アドレスをアカウントに追加すると、SendGrid は DNS 設定と DKIM 署名を直ちに更新します。 自動セキュリティをオフにした場合、送信ドメインを変更するたびに DKIM 署名を更新する責任があります。

**自動セキュリティが有効になっている例**:

```text
subdomain.mydomain.com. | CNAME | uxxxxxx.wl.sendgrid.net
s1._domainkey.mydomain.com. | CNAME | s1.domainkey.uxxxxxx.wl.sendgrid.net
s2._domainkey.mydomain.com. | CNAME | s2.domainkey.uxxxxxx.wl.sendgrid.net
```

**自動セキュリティが無効になっている例**:

```text
me12345.mydomain.com | MX | mx.sendgrid.net
me12345.mydomain.com | TXT | v=spf1 include:sendgrid.net ~all
m1._mydomain.com | TXT | k=rsa; t=s; p=<public-key>
```

ドメイン認証が設定されると、SendGrid は自動的にセキュリティポリシーフレームワーク（SPF）と DKIM レコードを処理します。 SendGrid が DNS レコードに追加する `CNAME` レコードを提供した後、SPF レコードを手動で管理しなくても、専用の IP アドレスを追加したり、他のアカウントを更新したりできます。 [Automated Security と DKIM 署名 ](https://docs.sendgrid.com/ui/account-and-settings/dkim-records#automated-security-and-your-dkim-signature) を参照してください。

DNS 設定をテストするには：

```
dig CNAME em.domain_name
dig CNAME s1._domainkey.domain_name
dig CNAME s2._domainkey.domain_name
```

## トランザクションメールのしきい値

トランザクションメールしきい値は、非実稼動環境から 1 か月に 12,000 通のメールなど、特定の期間内に Pro 環境から送信できるトランザクションメールメッセージの数を参照します。 しきい値は、スパムの送信と、メールの評判を損なう可能性のあるメールの送信を防ぐように設計されています。

送信者評判スコアが 95% を超える限り、実稼動環境で送信できるメールの数にハードリミットはありません。 評判は、バウンスメールまたは却下されたメールの数と、DNS ベースのスパムレジストリによってドメインが潜在的なスパムソースとしてフラグ設定されているかどうかによって影響を受けます。 [2}Commerce サポートナレッジベース ](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/emails-not-being-sent-sendgrid-credits-exceeded) の「Adobe Commerceで SendGrid のクレジットを超えてもメールが送信されない _を参照してください。_

**最大クレジットを超えたかどうかを確認するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. `/var/log/mail.log` に `authentication failed : Maxium credits exceeded` のエントリがないか確認します。

   `authentication failed` ログエントリが表示され、**メール送信のレピュテーション** が 95 以上である場合は、[Adobe Commerce サポートチケットを送信 ](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket) して、クレジット割り当ての引き上げをリクエストできます。

### メール送信の評価

メール送信の評価は、インターネットサービスプロバイダー（ISP）によって、メールメッセージを送信する会社に割り当てられるスコアです。 スコアが高いほど、ISP が受信者のインボックスにメッセージを配信する可能性が高くなります。 スコアが特定のレベルを下回ると、ISP が受信者のスパムフォルダーにメッセージをルーティングしたり、メッセージを完全に拒否したりする可能性があります。 評判スコアは、IP アドレスの 30 日間の平均が他の IP アドレスに対してランク付けされていることや、スパムの苦情率など、いくつかの要因によって決定されます。 [ メール送信の評判を確認する 8 つの方法 ](https://sendgrid.com/en-us/blog/5-ways-check-sending-reputation) を参照してください。
