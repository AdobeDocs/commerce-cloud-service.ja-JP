---
title: 環境の復元
description: クラウドインフラストラクチャプロジェクトからAdobe Commerceアプリケーションをアンインストールし、環境を安定した状態に復元する方法を説明します。
role: Developer
topic: Development
exl-id: b76bd6c3-986e-4adc-abd0-5b27db0d8a3b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '480'
ht-degree: 0%

---

# 環境の復元

統合環境で問題が発生し、 [有効なバックアップ](../storage/snapshots.md)次のいずれかの方法を使用して、環境を復元してみてください。

- Git ブランチのコードをリセットまたは元に戻します
- をアンインストールします。 [!DNL Commerce] アプリ
- 再デプロイメントを強制
- データベースを手動でリセット

{{stuck-deployment-tip}}

## Git ブランチのリセット

Git ブランチをリセットすると、コードが過去に安定した状態に戻ります。

**ブランチをリセットするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. Git コミット履歴を確認します。 用途 `--oneline` 1 行に短縮コミットを表示するには、次の手順に従います。

   ```bash
   git log --oneline
   ```

   レスポンスのサンプル：

   ```terminal
   6bf9f45 (HEAD -> master, magento/master, magento/develop, magento/HEAD, develop) Create composer.lock
   34d7434 2.4.6 upgrade
   b69803c Update composer.lock
   c1bca24 Add sample data
   ec604c3 Update magento/ece-tools
   ...
   ```

1. コードの最新の安定した状態を表すコミットハッシュを選択します。

   ブランチを元の初期化状態にリセットするには、ブランチを作成した最初のコミットを探します。 以下を使用できます。 `--reverse` 履歴を時系列の逆順に表示する。

1. ブランチをリセットするには、ハードリセットオプションを使用します。 このコマンドを使用する場合は、選択したコミット以降のすべての変更が破棄されるので、注意が必要です。

   ```bash
   git reset --hard <commit>
   ```

1. 変更を「再デプロイメントのトリガー」にプッシュし、Adobe Commerceを再インストールします。

   ```bash
   git push --force <origin> <branch>
   ```

## Commerce のアンインストール

のアンインストール [!DNL Commerce] アプリケーションは、データベースを復元し、デプロイメント設定を削除し、 `var/` サブディレクトリ。 また、このガイダンスでは、Git ブランチを以前の安定した状態にリセットします。 最近のバックアップがないが、SSH を使用してリモート環境にアクセスできる場合は、次の手順に従って環境を復元します。

- 設定管理を無効にする
- Adobe Commerceのアンインストール
- Git ブランチのリセット

Adobe Commerceソフトウェアのアンインストールにより、データベースが破棄および復元され、デプロイメント設定が削除され、 `var/` サブディレクトリ。 無効にすることが重要です。 [設定の管理](../store/store-settings.md) したがって、次回のデプロイ時に以前の設定が自動的に適用されることはありません。 次の項目を確認します。 `app/etc/` ディレクトリにが含まれていません `config.php` ファイル。

**Adobe Commerceソフトウェアをアンインストールするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. 設定ファイルを削除します。
   - Adobe Commerce 2.2 以降の場合：

     ```bash
     rm app/etc/config.php
     ```

   - Adobe Commerce 2.1 の場合：

     ```bash
     rm app/etc/config.local.php
     ```

1. Adobe Commerceアプリケーションをアンインストールします。

   ```bash
   php bin/magento setup:uninstall -n
   ```

1. Adobe Commerceが正常にアンインストールされたことを確認します。

   アンインストールが成功したことを確認する次のメッセージが表示されます。

   ```terminal
   [SUCCESS]: Magento uninstallation complete.
   ```

1. 次をクリア： `var/` サブディレクトリ。

   ```bash
   rm -rf var/*
   ```

1. ログアウトします。

>[!TIP]
>
>オプションで、ビルドキャッシュをクリーンアップすることをお勧めします。
>
>```bash
>magento-cloud project:clear-build-cache
>```

## 再デプロイメントを強制

Adobe Commerceのアンインストールを試みてもデプロイメントに失敗し続ける場合は、手動で再デプロイメントを強制してみてください。

```bash
git commit --allow-empty -m "<message>" && git push <origin> <branch>
```

## データベースをリセット

Adobe Commerceのアンインストールを試みたが、コマンドが失敗した場合や完了しなかった場合は、手動でデータベースをリセットできます。

**データベースをリセットするには**:

1. ローカルワークステーションで、プロジェクトディレクトリに移動します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. データベースに接続します。

   ```bash
   mysql -h database.internal
   ```

1. 次をドロップ： `main` データベース。

   ```shell
   drop database main;
   ```

1. 空の `main` データベース。

   ```shell
   create database main;
   ```

1. 次の設定ファイルを削除します。

   - `config.php`
   - `config.php.bak`
   - `env.php`
   - `env.php.bak`

1. ログアウトし、再デプロイメントをトリガーします。

   ```bash
   magento-cloud environment:redeploy
   ```

{{redeploy-warning}}
