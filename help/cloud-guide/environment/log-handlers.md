---
title: ログハンドラー
description: クラウドインフラストラクチャー上でAdobe Commerceのログハンドラーを設定する方法について説明します。
feature: Cloud, Logs, Configuration
role: Developer
exl-id: d3be7b6d-5778-4c32-865b-31bdb2852a23
source-git-commit: f8e35ecff4bcafda874a87642348e2d2bff5247b
workflow-type: tm+mt
source-wordcount: '232'
ht-degree: 0%

---

# ログハンドラー

ログハンドラーを設定して、メッセージをリモートログサーバーに送信できます。 ログハンドラーは、ログをSlackやメールにプッシュする方法と同様に、ログの作成とデプロイを他のシステムにプッシュします。 を有効にできます _syslog_ ハードウェア関連のメッセージをログに記録するのに最適なハンドラ、またはソフトウェア アプリケーションからのメッセージをログに記録するのに最適な Graylog Extended Log Format （GELF） ハンドラ。

次の例では、設定をに追加して、これらのハンドラーの両方を設定します。 `.magento.env.yaml` ファイル。 最小ログレベル（`min_level`）値については、を参照してください。 [ログレベル](#log-levels).

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

ログレベルは、通知メッセージの詳細レベルを決定します。 次のログレベルカテゴリには、その下のすべてのログレベルが含まれます。 例： `debug` level にはすべてのレベルからのログが含まれますが、 `alert` level は、アラートと緊急事態のみを表示します。

- **debug** – 詳細なデバッグ情報
- **情報**- ユーザー・ログインや SQL ログなどの対象イベント
- **通知** – 通常の、重要なイベント
- **警告** – 非推奨の API の使用や API の不適切な使用など、エラー以外の例外的な発生
- **エラー** – 即時アクションを必要としない実行時エラー
- **重大** – 使用できないアプリケーション・コンポーネントや予期しない例外などの重大な状態
- **アラート** SMS アラートをトリガーする、Web サイトのダウンやデータベースの使用不能など、即時対応が必要
- **緊急**— システムは使用できません
