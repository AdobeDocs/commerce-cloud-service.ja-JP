---
title: Redis サービスの設定
description: クラウドインフラストラクチャ上のAdobe Commerceのバックエンドキャッシュソリューションとして Redis を設定し、最適化する方法について説明します。
feature: Cloud, Cache, Services
exl-id: d6971875-d302-495a-ad10-a81c507c2bc9
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '255'
ht-degree: 0%

---

# Redis サービスの設定

[レディス](https://redis.io) は、Adobe Commerceがデフォルトで使用する Zend Framework Zend_Cache_Backend_File に代わる、オプションのバックエンドキャッシュソリューションです。

詳しくは、 [Redis を設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/redis/config-redis.html) （内） _設定ガイド_.

{{service-instruction}}

**Redis を有効にするには**:

1. 必要な名前とタイプを `.magento/services.yaml` ファイル。

   ```yaml
   myredis:
       type: redis:<version>
   ```

   独自の Redis 設定を提供するには、 `core_config` キーを `.magento/services.yaml` ファイル：

   ```yaml
   cache:
       type: redis:<version>
   ```

1. 関係を `.magento.app.yaml` ファイル。

   ```yaml
   runtime:
       extensions:
           - redis
   
   relationships:
       redis: "redis:redis"
   ```

1. コードの変更を追加、コミット、およびプッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml && git commit -m "Enable redis service" && git push origin <branch-name>
   ```

1. [サービスの関係を確認します](services-yaml.md#service-relationships).

{{service-change-tip}}

## Redis CLI の使用

Redis の関係に名前が付いていると仮定します。 `redis`を使用すると、 `redis-cli` ツールを使用します。

1. SSH を使用して、Redis がインストールおよび設定された統合環境に接続します。

1. ホストへの SSH トンネルを開きます。

   ```bash
   redis-cli -h redis.internal
   ```

## インストール済みの Redis バージョンを取得する

次のコマンドを使用して、統合環境に Redis バージョンをインストールします。

```bash
redis-cli -h redis.internal info | grep version
```

レスポンスのサンプル：

```terminal
redis_version:7.0.5
gcc_version:8.3.0
```

### Redis on Pro のステージングおよび実稼動環境

ステージング環境または実稼動環境に Redis バージョンをインストールするには、 `redis-server` コマンド：

```bash
redis-server -v
```

```terminal
Redis server v=7.0.5 ...
```

次のコマンドを使用して、Redis 設定を Pro Staging 環境または実稼動環境にインストールします。

```bash
echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
```

レスポンスのサンプル：

```terminal
"redis" : [
    {
        "cluster" : "project-master-123abc4",
        "fragment" : null,
        "host" : "redis.internal",
        "host_mapped" : false,
        "hostname" : "oblahblahblahblahe.redis.service._.magentosite.cloud",
        "ip" : "169.254.10.10",
        "password" : null,
        "path" : null,
        "port" : 6379,
        "public" : false,
        "query" : {},
        "rel" : "redis",
        "scheme" : "redis",
        "service" : "redis",
        "type" : "redis:7.0.5",
        "username" : null
    }
]
```

## Redis のトラブルシューティング

Redis の問題のトラブルシューティングに関するヘルプについては、次のAdobe Commerceサポート記事を参照してください。

- [Redis の問題が管理者のログインまたはチェックアウトを遅らせる](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/redis-issue-delay-magento-admin-login-or-checkout.html)
- [拡張 Redis キャッシュ実装Adobe Commerce 2.3.5 以降](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/planning/redis-service-configuration.html)
- [MDVA-30102: Redis キャッシュがいっぱいになっています](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/patches/v1-0-6/mdva-30102-magento-patch-redis-cache-getting-full.html)
- [Adobe Commerceに関する Managed Alerts:Redis メモリ警告アラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-on-magento-commerce-redis-memory-warning-alert.html)
- [Adobe Commerceの Managed Alerts:Redis Memory Critical アラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-on-magento-commerce-redis-memory-critical-alert.html)
- [Redis のトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/redis-troubleshooter.html)
