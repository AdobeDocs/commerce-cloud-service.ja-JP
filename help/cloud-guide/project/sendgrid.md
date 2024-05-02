---
title: SendGrid メールサービス
description: クラウドインフラストラクチャ上のAdobe Commerce用 SendGrid メールサービスと、DNS 設定をテストする方法について説明します。
exl-id: 30d3c780-603d-4cde-ab65-44f73c04f34d
source-git-commit: 2b106edcaaacb63c0e785f094b7e1b755885abd0
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
>アカウントの SendGrid の詳細については、次を参照してください [オンボーディング UI](https://cloud.magento.com) を選択し、 **プロジェクト詳細** > **ホスティング情報** タブ。

## メールを有効または無効にする

Cloud Console またはコマンドラインから、各環境の送信メールを有効または無効にすることができます。

デフォルトでは、実稼動環境およびステージング環境では、送信メールが有効になっています。 ただし、 [!UICONTROL Outgoing emails] を設定するまで、環境設定で無効と表示されることがあります。 `enable_smtp` を使用したプロパティ [コマンドライン](outgoing-emails.md#enable-emails-in-the-cli) または [クラウドコンソール](outgoing-emails.md#enable-emails-in-the-cloud-console). 統合およびステージング環境で送信メールを有効にして、クラウドプロジェクトのユーザーに対して二要素認証またはパスワードリセットのメールを送信できます。 参照： [テスト用メールの設定](outgoing-emails.md).

実稼動環境またはステージング環境で送信メールを無効にするか再度有効にする必要がある場合は、 [Adobe Commerce サポートチケット](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide).

>[!TIP]
>
>を更新中 [!UICONTROL enable_smtp] プロパティ値の基準 [コマンドライン](outgoing-emails.md#enable-emails-in-the-cli) 次も変更： [!UICONTROL Enable outgoing emails] でこの環境の値を設定しています [クラウドコンソール](outgoing-emails.md#enable-emails-in-the-cloud-console).

## SendGrid ダッシュボード

すべてのクラウドプロジェクトは中央アカウントで管理されるので、SendGrid ダッシュボードにアクセスできるのはサポートのみです。 SendGrid は、サブアカウント制限機能を提供していません。

アクティビティログで配信ステータス、バウンスメールアドレス、却下メールアドレス、ブロックされたメールアドレスのリストを確認するには、 [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket). サポートチーム **できません** 30 日を経過したアクティビティログを取得します。

可能であれば、次の情報をリクエストに含めます。

* 影響を受ける 1 つまたは複数のメールアドレス
* 対象の期間（過去 30 日以内のみ）
* 電子メールの件名

## DomainKeys Identified Mail （DKIM）

DKIM は、インターネットサービスプロバイダー（ISP）が正当な送信者アドレスと偽物の送信者アドレスの両方を識別できるようにするメール認証技術です。これは、フィッシングやメール詐欺で一般的に使用される技術です。 DKIM は、DNS レコードを管理するドメイン所有者に依存しています。 DKIM を使用する場合、送信者サーバーは秘密鍵を使用してメッセージに署名します。 また、ドメイン所有者が DKIM レコードを追加します。このレコードは変更されています `TXT` レコード。送信者ドメインの DNS レコードに送信されます。 この `TXT` レコードには、受信者のメールサーバーがメッセージの署名を検証するために使用する公開鍵が含まれています。 DKIM 公開鍵暗号手順を使用すると、受信者は送信者の信頼性を検証できます。 参照： [DKIM Records の説明](https://docs.sendgrid.com/ui/account-and-settings/dkim-records).

>[!WARNING]
>
>SendGrid DKIM 署名とドメイン認証のサポートは、スタータープロジェクトではなく、Pro プロジェクトでのみ使用できます。 その結果、送信トランザクションメールはスパムフィルターによってフラグ付けされる可能性が高くなります。 DKIM を使用すると、認証済みメール送信者としての配信率が向上します。 メッセージ配信率を向上させるには、Starter から Pro にアップグレードするか、独自の SMTP サーバーまたはメール配信サービスプロバイダーを使用します。 参照： [メール接続の設定](https://experienceleague.adobe.com/en/docs/commerce-admin/systems/communications/email-communications) が含まれる _管理システムガイド_.

### 送信者とドメインの認証

SendGrid が実稼動環境またはステージング環境からユーザーに代わってトランザクションメールを送信するには、3 つの SendGrid サブドメイン DNS エントリを含めるように DNS 設定を設定する必要があります。 各 SendGrid アカウントには、一意のが割り当てられます `TXT` 送信メールの認証に使用されるレコード。

**ドメイン認証を有効にするには**:

1. を送信 [サポートチケット](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket) 特定のドメインに対する DKIM の有効化をリクエストするには：（**ステージング環境および実稼動環境のみ**）に設定します。
1. で DNS 設定を更新します。 `TXT` および `CNAME` サポートチケットで提供されたレコード。

**例 `TXT` アカウント ID を持つレコード**:

```text
v=spf1 include:u17504801.wl.sendgrid.net -all
```

**例 `CNAME` レコード**:

| ドメイン | 参照先 | レコードタイプ |
| ---------- | ---------- | ------------- |
| em.emaildomain.com | uxxxxxx.wl.sendgrid.net | CNAME |
| s1。_domainkey.emaildomain.com | s1.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |
| s2。_domainkey.emaildomain.com | s2.domainkey.uxxxxxx.wl.sendgrid.net | CNAME |

### DKIM 署名と自動セキュリティ

認証済みドメインを設定する際に、自動セキュリティと手動セキュリティのどちらかを選択できます。 自動セキュリティを選択した場合、SendGrid は DKIM と SPF レコードを自動的に管理します。 新しい送信専用 IP アドレスをアカウントに追加すると、SendGrid は DNS 設定と DKIM 署名を直ちに更新します。 自動セキュリティをオフにした場合、送信ドメインを変更するたびに DKIM 署名を更新する責任があります。

**有効化された自動セキュリティの例**:

```text
subdomain.mydomain.com. | CNAME | uxxxxxx.wl.sendgrid.net
s1._domainkey.mydomain.com. | CNAME | s1.domainkey.uxxxxxx.wl.sendgrid.net
s2._domainkey.mydomain.com. | CNAME | s2.domainkey.uxxxxxx.wl.sendgrid.net
```

**自動セキュリティ無効化の例**:

```text
me12345.mydomain.com | MX | mx.sendgrid.net
me12345.mydomain.com | TXT | v=spf1 include:sendgrid.net ~all
m1._mydomain.com | TXT | k=rsa; t=s; p=<public-key>
```

ドメイン認証が設定されると、SendGrid は自動的にセキュリティポリシーフレームワーク（SPF）と DKIM レコードを処理します。 SendGrid が以下を提供した後 `CNAME` レコード DNS レコードに追加する場合は、SPF レコードを手動で管理しなくても、専用の IP アドレスを追加したり、その他のアカウントを更新したりできます。 参照： [自動セキュリティと DKIM 署名](https://docs.sendgrid.com/ui/account-and-settings/dkim-records#automated-security-and-your-dkim-signature).

DNS 設定をテストするには：

```terminal
dig CNAME em.domain_name
dig CNAME s1._domainkey.domain_name
dig CNAME s2._domainkey.domain_name
```

## トランザクションメールのしきい値

トランザクションメールしきい値は、非実稼動環境から 1 か月に 12,000 通のメールなど、特定の期間内に Pro 環境から送信できるトランザクションメールメッセージの数を参照します。 しきい値は、スパムの送信と、メールの評判を損なう可能性のあるメールの送信を防ぐように設計されています。

送信者評判スコアが 95% を超える限り、実稼動環境で送信できるメールの数にハードリミットはありません。 評判は、バウンスメールまたは却下されたメールの数と、DNS ベースのスパムレジストリによってドメインが潜在的なスパムソースとしてフラグ設定されているかどうかによって影響を受けます。 参照： [Adobe Commerceで SendGrid クレジットを超えてもメールが送信されない](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/emails-not-being-sent-sendgrid-credits-exceeded) が含まれる _コマースサポートのナレッジベース_.

**最大クレジットを超えたかどうかを確認するには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. を確認します `/var/log/mail.log` （用） `authentication failed : Maxium credits exceeded` エントリ。

   次のいずれかが表示された場合： `authentication failed` ログエントリと **メール送信の評価** は少なくとも 95 ですが、次のことが可能です [Adobe Commerce サポートチケットを送信](https://experienceleague.adobe.com/en/docs/commerce-knowledge-base/kb/help-center-guide/magento-help-center-user-guide#submit-ticket) 信用割当の増額を請求する。

### メール送信の評価

メール送信の評価は、インターネットサービスプロバイダー（ISP）によって、メールメッセージを送信する会社に割り当てられるスコアです。 スコアが高いほど、ISP が受信者のインボックスにメッセージを配信する可能性が高くなります。 スコアが特定のレベルを下回ると、ISP が受信者のスパムフォルダーにメッセージをルーティングしたり、メッセージを完全に拒否したりする可能性があります。 評判スコアは、IP アドレスの 30 日間の平均が他の IP アドレスに対してランク付けされていることや、スパムの苦情率など、いくつかの要因によって決定されます。 参照： [メール送信の評価を確認する 8 つの方法](https://sendgrid.com/en-us/blog/5-ways-check-sending-reputation).
