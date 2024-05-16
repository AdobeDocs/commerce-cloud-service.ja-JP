---
title: 通知の設定
description: クラウドインフラストラクチャ環境でAdobe Commerceの通知を設定する方法について説明します。
feature: Cloud, Configuration, Logs
exl-id: be48abcd-4753-4e89-83fe-0aadfb0415c2
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '405'
ht-degree: 0%

---

# 通知の設定

デフォルトでは、クラウドインフラストラクチャー上のAdobe Commerceによって、ビルドおよびデプロイのアクションがに書き込まれます `app/var/log/cloud.log` Adobe Commerce ルートアプリケーションディレクトリ内のファイル。 オプションで、Slackやメールなどのメッセージングシステムにログを送信して、リアルタイムで通知を受け取ることができます。

例えば、デプロイメントが失敗したときにユーザーのグループにアラートを送信するSlackメッセージを送信し、何が問題だったのかを調査するように促すことができます。

## プラン通知

通知を設定する前に、次の点を考慮してください。

- どのような通知（Slackメッセージ、メール、その両方）を受信しますか？
- ログに表示する詳細の量
- 通知をどこに設定しますか（統合、ステージング、実稼動）?

例えば、初期開発時には、統合環境に関する詳細なログを示すメール通知を使用して、ステージング環境にデプロイする前に問題をデバッグするのに役立てることができます。 ステージング環境または実稼動環境にデプロイする準備が整ったら、詳細情報の少ないSlackメッセージの方が望ましい場合があります。

>[!NOTE]
>
>通知の設定に使用される設定ファイルはプロジェクトディレクトリのルートにあるので、変更を任意の環境にプッシュする場合に適用されます。 環境ごとに通知をカスタマイズする場合は、設定ファイルを変更してから、その環境にプッシュする必要があります。

## 通知の設定

通知を設定するには：

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。
1. が含まれる `.magento.env.yaml` ファイルをプロジェクトルートに作成し、優先通知を含むメッセージングシステムの設定を追加します。 [ログレベル](log-handlers.md#log-levels).

   例えば、と両方のSlackを設定するには、次のようにします _および_ メールの設定は、以下を使用します。

   ```yaml
   log:
     slack:
       token: "<your-slack-token>"
       channel: "<your-slack-channel>"
       username: "SlackHandler"
       min_level: "info"
     email:
       to: <your-email>
       from: <your-email>
       subject: "Log notification from Adobe Commerce"
       min_level: "notice"
   ```

   >[!NOTE]
   >
   >クラウドインフラストラクチャー上のAdobe Commerceでは、デプロイメントフェーズでのみメールを送信します。

1. 変更内容をコミットし、リモートサーバーにプッシュします。

   ```bash
   git -A && git commit -m "Configure build/deploy notifications"
   ```

   ```bash
   git push origin <branch-name>
   ```

### Slack設定の例

次の例は、Slackのみの設定を示しています。

```yaml
log:
  slack:
    token: "<your-slack-token>"
    channel: "<your-slack-channel>"
    username: "SlackHandler"
    min_level: "info"
```

- `token` – あなたのSlack [ユーザートークン](https://api.slack.com/docs/token-types#user). ユーザートークンは、クラウドインフラストラクチャでAdobe Commerceに対してメッセージの送信を認証します。
- `channel`- クラウドインフラストラクチャ上のAdobe Commerceが通知を送信するSlackチャネルの名前。
- `username`- クラウドインフラストラクチャー上のAdobe CommerceがSlackで通知メッセージを送信する際に使用するユーザー名。
- `min_level` – 通知メッセージの最小ログレベル。 を使用することをお勧めします。 `info`.

### メール設定の例

次に、メールのみの設定の例を示します。

>[!NOTE]
>
>クラウドインフラストラクチャー上のAdobe Commerceでは、デプロイメントフェーズでのみメールを送信します。

```yaml
log:
  email:
    to: <your-email>
    from: <your-email>
    subject: "Log notification from Adobe Commerce"
    min_level: "notice"
```

- `to`— クラウドインフラストラクチャ上のAdobe Commerceが通知メッセージを送信するメールアドレス
- `from` – 受信者に通知メッセージを送信するためのメールアドレス。
- `subject`- メールの説明。
- `min_level` – 通知メッセージの最小ログレベル。 を使用することをお勧めします。 `notice` または `warning`.
