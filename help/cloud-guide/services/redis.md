---
title: Redis サービスの設定
description: クラウドインフラストラクチャー上のAdobe Commerceのバックエンドキャッシュソリューションとして Redis を設定し最適化する方法について説明します。
feature: Cloud, Cache, Services
exl-id: d6971875-d302-495a-ad10-a81c507c2bc9
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '255'
ht-degree: 0%

---

# Redis サービスの設定

[Redis](https://redis.io) は、Adobe Commerceがデフォルトで使用する Zend フレームワークの Zend_Cache_Backend_File に代わる、オプションのバックエンドキャッシュソリューションです。

参照： [Redis の設定](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cache/redis/config-redis.html) が含まれる _設定ガイド_.

{{service-instruction}}

**Redis を有効にするには**:

1. 必要な名前とタイプをに追加します。 `.magento/services.yaml` ファイル。

   ```yaml
   myredis:
       type: redis:<version>
   ```

   独自の Redis 設定を指定するには、を追加します。 `core_config` のキー `.magento/services.yaml` ファイル：

   ```yaml
   cache:
       type: redis:<version>
   ```

1. での関係の設定 `.magento.app.yaml` ファイル。

   ```yaml
   runtime:
       extensions:
           - redis
   
   relationships:
       redis: "redis:redis"
   ```

1. コードの変更を追加、コミット、プッシュします。

   ```bash
   git add .magento/services.yaml .magento.app.yaml && git commit -m "Enable redis service" && git push origin <branch-name>
   ```

1. [サービス関係の検証](services-yaml.md#service-relationships).

{{service-change-tip}}

## Redis CLI の使用

Redis 関係にという名前を付けた場合 `redis`にアクセスするには、 `redis-cli` ツール。

1. SSH を使用して、Redis がインストールおよび設定された統合環境に接続します。

1. ホストへの SSH トンネルを開きます。

   ```bash
   redis-cli -h redis.internal
   ```

## インストールされた Redis バージョンを取得します。

次のコマンドを使用して、統合環境にインストールされている Redis のバージョンを取得します。

```bash
redis-cli -h redis.internal info | grep version
```

応答の例：

```terminal
redis_version:7.0.5
gcc_version:8.3.0
```

### Redis on Pro ステージング環境と実稼動環境

ステージング環境または実稼動環境に Redis バージョンをインストールするには、を使用します。 `redis-server` コマンド：

```bash
redis-server -v
```

```terminal
Redis server v=7.0.5 ...
```

次のコマンドを使用して、Redis 設定を Pro ステージング環境または実稼動環境にインストールします。

```bash
echo $MAGENTO_CLOUD_RELATIONSHIPS | base64 -d | json_pp
```

応答の例：

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

Redis の問題のトラブルシューティングについては、次のAdobe Commerce サポート記事を参照してください。

- [Redis 問題の管理者ログインまたはチェックアウトの遅延](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/redis-issue-delay-magento-admin-login-or-checkout.html)
- [Adobe Commerce 2.3.5 以降の拡張 Redis キャッシュ実装](https://experienceleague.adobe.com/docs/commerce-operations/implementation-playbook/best-practices/planning/redis-service-configuration.html)
- [MDVA-30102: Redis キャッシュがいっぱいです](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/patches/v1-0-6/mdva-30102-magento-patch-redis-cache-getting-full.html)
- [Adobe Commerceの管理アラート：Redis メモリ警告アラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-on-magento-commerce-redis-memory-warning-alert.html)
- [Adobe Commerceの管理アラート：Redis メモリクリティカルアラート](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/support-tools/managed-alerts/managed-alerts-on-magento-commerce-redis-memory-critical-alert.html)
- [Redis のトラブルシューティング](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/redis-troubleshooter.html)
