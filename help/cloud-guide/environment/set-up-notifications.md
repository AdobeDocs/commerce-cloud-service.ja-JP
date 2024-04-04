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

デフォルトでは、Adobe Commerce on cloud infrastructure は、ビルドアクションを `app/var/log/cloud.log` ファイルをAdobe Commerceのルートアプリケーションディレクトリ内に配置します。 必要に応じて、Slackや電子メールなどのメッセージングシステムにログを送信して、リアルタイム通知を受け取ることができます。

例えば、デプロイメントが失敗した場合にユーザーのグループに警告するSlackメッセージを送信し、問題の調査を促すことができます。

## プラン通知

通知を設定する前に、次の点を考慮してください。

- どのような通知を受信しますか (Slackメッセージ、E メール、その両方 )?
- ログに表示する詳細の数
- 通知（統合、ステージング、実稼動）をどこで設定しますか？

例えば、初回開発時に、ステージング環境にデプロイする前に問題をデバッグするのに役立つように、統合環境に関する詳細なログを表示する電子メール通知を希望する場合があります。 ステージング環境または実稼動環境にデプロイする準備が整ったら、より詳細な情報を含むSlackメッセージを使用することをお勧めします。

>[!NOTE]
>
>通知の設定に使用する設定ファイルは、プロジェクトディレクトリのルートにあるので、変更を任意の環境にプッシュした場合に適用されます。 環境ごとに通知をカスタマイズする場合は、設定ファイルをその環境にプッシュする前に変更する必要があります。

## 通知の設定

通知を設定するには：

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。
1. Adobe Analytics の `.magento.env.yaml` ファイルをプロジェクトルートに追加し、希望の通知を含むメッセージングシステム設定を追加します。 [ログレベル](log-handlers.md#log-levels).

   例えば、両方のSlackを設定するには _および_ e メール設定には、以下を使用します。

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
   >Adobe Commerce on cloud infrastructure は、デプロイメントフェーズ中にのみ E メールを送信します。

1. 変更をリモートサーバーにコミットしてプッシュします。

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

- `token`—Slack [ユーザートークン](https://api.slack.com/docs/token-types#user). ユーザートークンは、クラウドインフラストラクチャ上のAdobe Commerceに対し、メッセージの送信を許可します。
- `channel` — クラウドインフラストラクチャ上のSlackチャネルAdobe Commerceの名前が通知を送信します。
- `username` — クラウドインフラストラクチャ上のAdobe Commerceのユーザー名。通知メッセージをSlackで送信する際に使用します。
- `min_level` — 通知メッセージの最小ログレベル。 次を使用することをお勧めします。 `info`.

### 電子メール設定の例

次の例は、電子メールのみの設定を示しています。

>[!NOTE]
>
>Adobe Commerce on cloud infrastructure は、デプロイメントフェーズ中にのみ E メールを送信します。

```yaml
log:
  email:
    to: <your-email>
    from: <your-email>
    subject: "Log notification from Adobe Commerce"
    min_level: "notice"
```

- `to` — クラウドインフラストラクチャ上のAdobe Commerceのメールアドレスが通知メッセージを送信します。
- `from` — 受信者に通知メッセージを送信する電子メールアドレス。
- `subject` — 電子メールの説明。
- `min_level` — 通知メッセージの最小ログレベル。 次を使用することをお勧めします。 `notice` または `warning`.
