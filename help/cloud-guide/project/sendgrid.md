---
title: SendGrid 電子メールサービス
description: クラウドインフラストラクチャ上のAdobe Commerceの SendGrid 電子メールサービスと、DNS 設定をテストする方法について説明します。
exl-id: 30d3c780-603d-4cde-ab65-44f73c04f34d
source-git-commit: 7c22dc3b0e736043a3e176d2b7ae6c9dcbbf1eb5
workflow-type: tm+mt
source-wordcount: '1019'
ht-degree: 0%

---

# SendGrid 電子メールサービス

SendGrid Simple Mail Transfer Protocol(SMTP) プロキシサービスは、次のサポートを含む、送信メール認証および評判監視サービスを提供します。

* すべてのアウトバウンドトランザクション E メール
* 専用 IP アドレス
* E メールドメイン検証用のドメイン登録、DomainKeys 識別メール (DKIM) 署名（Pro Staging および実稼動環境のみ）
* カスタムドメインの登録（Pro のみ）
* Starter と Pro の統合環境の自動統合。 実稼動環境とステージング環境では、Infrastructure as a Service(IaaS) ハードウェアプロビジョニングプロセス中に手動でプロビジョニングと設定を行う必要があります

SendGrid SMTP プロキシは、受信メールを受信する汎用の電子メールサーバーとして、または電子メールマーケティングキャンペーンで使用するためのものではありません。

>[!TIP]
>
>アカウントの SendGrid の詳細は、 [オンボーディング UI](https://cloud.magento.com) をクリックし、 **プロジェクトの詳細** > **ホスティング情報** タブをクリックします。

## 電子メールを有効または無効にする

デフォルトでは、送信電子メールは、Pro 実稼動環境およびステージング環境で有効になっています。 The [!UICONTROL Outgoing emails] を設定するまで、のステータスに関係なく、環境設定に表示される場合があります。 `enable_smtp` プロパティ。 他の環境向けに送信する電子メールを有効にして、Cloud プロジェクトユーザーに 2 要素の認証電子メールを送信できます。 詳しくは、 [テスト用の電子メールの設定](outgoing-emails.md).

## SendGrid ダッシュボード

すべてのクラウドプロジェクトは、中央のアカウントで管理されるので、SendGrid ダッシュボードにアクセスできるのはサポートのみです。 SendGrid は、サブアカウント制限機能を提供しません。

アクティビティログで配信ステータスや、バウンスメールアドレス、拒否メールアドレス、ブロックメールアドレスのリストを確認するには、次の手順を実行します。 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket). サポートチーム **できません** 30 日より古いアクティビティログを取得します。

可能な場合は、リクエストに次の情報を含めます。

* 影響を受ける E メールアドレスまたはアドレス
* 対象の期間（過去 30 日以内のみ）
* 電子メールの件名

## DomainKeys 識別メール (DKIM)

DKIM は、インターネットサービスプロバイダー (ISP) が正規の送信者アドレスと偽の送信者アドレスの両方を識別できる E メール認証テクノロジーで、フィッシング詐欺や E メール詐欺で一般的に使用される手法です。 DKIM は、DNS レコードを管理するドメイン所有者に依存しています。 DKIM を使用する場合、送信者サーバーは秘密鍵を使用してメッセージに署名します。 また、ドメイン所有者が DKIM レコードを追加します。これは変更済みです `TXT` レコードを送信者ドメインの DNS レコードに送信します。 この `TXT` レコードには、受信者メールサーバーがメッセージの署名を検証するために使用する公開鍵が含まれています。 DKIM 公開鍵暗号化手順を使用すると、受信者は送信者の信頼性を検証できます。 詳しくは、 [DKIM レコードの説明](https://docs.sendgrid.com/ui/account-and-settings/dkim-records).

>[!WARNING]
>
>SendGrid DKIM 署名とドメイン認証のサポートは、Pro プロジェクトでのみ利用でき、Starter では利用できません。 その結果、送信トランザクション E メールはスパムフィルターによってフラグ付けされる可能性が高くなります。 DKIM を使用すると、認証済みの E メール送信者としての配信率が向上します。 メッセージ配信率を向上させるには、Starter から Pro にアップグレードするか、独自の SMTP サーバーまたは E メール配信サービスプロバイダーを使用します。 詳しくは、 [電子メール接続を設定する](https://experienceleague.adobe.com/docs/commerce-admin/systems/communications/email-communications.html) （内） _管理システムガイド_.

### 送信者とドメインの認証

SendGrid が実稼動またはステージング環境から自分に代わってトランザクション E メールを送信するには、3 つの SendGrid サブドメイン DNS エントリを含むように DNS 設定を設定する必要があります。 各 SendGrid アカウントには、一意の `TXT` 送信 e メールの認証に使用されるレコード。

**ドメイン認証を有効にするには**:

1. を送信 [サポートチケット](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 特定のドメイン (**プロステージング環境と実稼動環境のみ**) をクリックします。
1. DNS 設定を `TXT` および `CNAME` サポートチケットで提供されたレコード。

**例 `TXT` アカウント ID を持つレコード**:

```text
v=spf1 include:u17504801.wl.sendgrid.net -all
```

**例 `CNAME` レコード**:

| ドメイン | ポイント先 | レコードタイプ |
| ---------- | ---------- | ------------- |
| em.emaildomain.com | uxxxxxx.wl.sendgrid.net | CNAME |
| s1._domainkey.emaildomain.com | s1.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |
| s2._domainkey.emaildomain.com | s2.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |

### DKIM 署名と自動セキュリティ

認証済みドメインの設定時に、自動セキュリティと手動セキュリティのどちらを選択するかを選択できます。 自動セキュリティを選択した場合、SendGrid は DKIM および SPF レコードを自動的に管理します。 新しい専用の送信 IP アドレスをアカウントに追加すると、SendGrid は DNS 設定と DKIM 署名を直ちに更新します。 自動セキュリティをオフにした場合、送信ドメインを変更するたびに DKIM 署名を更新する必要があります。

**自動セキュリティの有効化の例**:

```text
subdomain.mydomain.com. | CNAME | uxxxxxx.wl.sendgrid.net
s1._domainkey.mydomain.com. | CNAME | s1.domainkey.uxxxxxx.wl.sendgrid.net
s2._domainkey.mydomain.com. | CNAME | s2.domainkey.uxxxxxx.wl.sendgrid.net
```

**自動セキュリティ無効の例**:

```text
me12345.mydomain.com | MX | mx.sendgrid.net
me12345.mydomain.com | TXT | v=spf1 include:sendgrid.net ~all
m1._mydomain.com | TXT | k=rsa; t=s; p=<public-key>
```

ドメイン認証が設定されると、SendGrid はセキュリティポリシーフレームワーク (SPF) および DKIM レコードを自動的に処理します。 SendGrid が `CNAME` レコードを DNS レコードに追加すると、SPF レコードを手動で管理する必要なく、専用の IP アドレスを追加して、その他のアカウントを更新できます。 詳しくは、 [自動セキュリティと DKIM 署名](https://docs.sendgrid.com/ui/account-and-settings/dkim-records#automated-security-and-your-dkim-signature).

DNS 設定をテストするには：

```terminal
dig CNAME em.domain_name
dig CNAME s1._domainkey.domain_name
dig CNAME s2._domainkey.domain_name
```

## トランザクション E メールしきい値

トランザクション E メールしきい値は、実稼動環境以外からの 1 ヶ月あたり 12,000 通の E メールなど、特定の期間内に Pro 環境から送信できるトランザクション E メールメッセージの数を指します。 しきい値は、スパムの送信を防ぎ、E メールの評判を損なう可能性があるように設計されています。

送信者のレピュテーションスコアが 95%を超える限り、実稼動環境で送信できる E メールの数に厳しい制限はありません。 レピュテーションは、バウンスメールまたは却下された E メールの数や、DNS ベースのスパムレジストリがお客様のドメインを潜在的なスパムソースとしてフラグ付けしているかどうかの影響を受けます。 詳しくは、 [Adobe Commerceで SendGrid のクレジットが超過した場合、電子メールは送信されませんでした](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/emails-not-being-sent-sendgrid-credits-exceeded.html) （内） _コマースサポートナレッジベース_.

**最大クレジット数を超えたかどうかを確認するには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. 次を確認します。 `/var/log/mail.log` 対象： `authentication failed : Maxium credits exceeded` エントリ。

   見つかった場合 `authentication failed` ログエントリと **E メール送信レピュテーション** が 95 以上の場合は、 [Adobe Commerceサポートチケットを送信する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide.html#submit-ticket) 与信割当増額を請求する。

### E メール送信レピュテーション

E メール送信レピュテーションとは、E メールメッセージを送信する会社に対して、インターネットサービスプロバイダ (ISP) によって割り当てられたスコアです。 スコアが高いほど、ISP が受信者の受信ボックスにメッセージを配信する可能性が高くなります。 スコアが特定のレベルを下回った場合、ISP は、受信者のスパムフォルダーにメッセージをルーティングしたり、メッセージを完全に拒否したりする場合があります。 レピュテーションスコアは、他の IP アドレスやスパムの苦情率に対する IP アドレスの平均が 30 日間であるなど、いくつかの要因によって決定されます。 詳しくは、 [送信レピュテーションを確認する 5 つの方法](https://sendgrid.com/blog/5-ways-check-sending-reputation/).
