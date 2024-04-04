---
title: ログハンドラー
description: クラウドインフラストラクチャ上のAdobe Commerceのログハンドラーを設定する方法について説明します。
feature: Cloud, Logs, Configuration
role: Developer
exl-id: d3be7b6d-5778-4c32-865b-31bdb2852a23
source-git-commit: f8e35ecff4bcafda874a87642348e2d2bff5247b
workflow-type: tm+mt
source-wordcount: '232'
ht-degree: 0%

---

# ログハンドラー

リモートログサーバーにメッセージを送信するようにログハンドラーを設定できます。 ログハンドラーは、ログをSlackや電子メールにプッシュするのと同様に、ビルドログを他のシステムにプッシュしてデプロイします。 以下を有効にすると、 _syslog_ ハードウェアに関連するメッセージのログ記録に最適なハンドラ、またはソフトウェアアプリケーションからのメッセージのログ記録に最適な Graylog Extended Log Format (GELF) ハンドラ。

次の例では、 `.magento.env.yaml` ファイル。 最小ログレベル (`min_level`) の値については、 [ログレベル](#log-levels).

```yaml
log:
  syslog:
    ident: "<syslog-ident>"
    facility: 8 # https://php.net/manual/en/network.constants.php
    min_level: "info"
    logopts: <syslog-logopts>

  syslog_udp:
    host: "<syslog-host>"
    port: <syslog-port>
    facility: 8  # https://php.net/manual/en/network.constants.php
    ident: "<syslog-ident>"
    min_level: "info"

  gelf:
    min_level: "info"
    use_default_formatter: true
    additional: # Some additional information for each log message
      project: "<some-project-id>"
      app_id: "<some-app-id>"
    transport:
      http:
        host: "<http-host>"
        port: <http-port>
        path: "<http-path>"
        connection_timeout: 60
      tcp:
        host: "<tcp-host>"
        port: <tcp-port>
        connection_timeout: 60
      udp:
        host: "<udp-host>"
        port: <udp-port>
        chunk_size: 1024
```

## ログレベル

ログレベルは、通知メッセージの詳細レベルを決定します。 次のログレベルカテゴリには、その下のすべてのログレベルが含まれます。 例えば、 `debug` レベルには、すべてのレベルからのログが含まれますが、 `alert` 「レベル」には、アラートと緊急事態のみが表示されます。

- **デバッグ** — 詳細なデバッグ情報
- **情報** — ユーザー・ログインや SQL ログなどの注目のイベント
- **通知** — 通常の、重要なイベント。
- **警告** — 非推奨（廃止予定）の API の使用や API の使用不足など、エラーではない例外的な発生
- **エラー** — すぐに行動を起こす必要のない実行時のエラー
- **重要な** — 使用できないアプリケーションコンポーネントや予期しない例外など、重要な条件
- **アラート**- SMS アラートをトリガーするために、Web サイトがダウンしたり、データベースが使用できないなどの即時のアクションが必要です。
- **緊急** — システムが使用できない
