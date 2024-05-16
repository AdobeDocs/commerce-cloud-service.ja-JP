---
title: 環境の復元
description: クラウドインフラストラクチャプロジェクトからAdobe Commerce アプリケーションをアンインストールし、環境を安定した状態に復元する方法について説明します。
role: Developer
topic: Development
exl-id: b76bd6c3-986e-4adc-abd0-5b27db0d8a3b
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '480'
ht-degree: 0%

---

# 環境の復元

統合環境で問題が発生し、に情報がない場合 [有効なバックアップ](../storage/snapshots.md)、次のいずれかの方法を使用して環境を復元してみてください。

- Git ブランチのコードをリセットまたは元に戻します
- をアンインストール [!DNL Commerce] 適用
- 再デプロイメントを強制的に実行
- データベースを手動でリセット

{{stuck-deployment-tip}}

## Git ブランチをリセット

Git ブランチをリセットすると、コードが以前に安定した状態に戻ります。

**分岐をリセットするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. Git コミット履歴を確認します。 使用方法 `--oneline` 短縮されたコミットを 1 行で表示するには：

   ```bash
   git log --oneline
   ```

   応答の例：

   ```terminal
   6bf9f45 (HEAD -> master, magento/master, magento/develop, magento/HEAD, develop) Create composer.lock
   34d7434 2.4.6 upgrade
   b69803c Update composer.lock
   c1bca24 Add sample data
   ec604c3 Update magento/ece-tools
   ...
   ```

1. コードの最後の既知の安定状態を表すコミットハッシュを選択します。

   ブランチを元の初期化された状態にリセットするには、ブランチを作成した最初のコミットを見つけます。 次を使用できます `--reverse` 履歴を時系列の逆順に表示する。

1. ハードリセットオプションを使用して、ブランチをリセットします。 このコマンドを使用する場合は、選択したコミット以降の変更がすべて破棄されるので注意が必要です。

   ```bash
   git reset --hard <commit>
   ```

1. 変更をプッシュして再デプロイメントをトリガーにします。これにより、Adobe Commerceが再インストールされます。

   ```bash
   git push --force <origin> <branch>
   ```

## Commerceのアンインストール

アンインストール， [!DNL Commerce] アプリケーションは、データベースを復元し、デプロイメント設定を削除して、 `var/` サブディレクトリ。 また、このガイダンスでは、Git ブランチを以前の安定した状態にリセットします。 最新のバックアップがなくても、SSH を使用してリモート環境にアクセスできる場合は、次の手順に従って環境を復元します。

- 構成管理を無効にする
- Adobe Commerceのアンインストール
- Git ブランチをリセットします

Adobe Commerce ソフトウェアをアンインストールすると、データベースが削除されて復元され、配置設定が削除されてクリアされます `var/` サブディレクトリ。 無効にすることが重要です [設定管理](../store/store-settings.md) これにより、次回のデプロイメント時に以前の設定が自動的に適用されなくなります。 次のことを確認します `app/etc/` ディレクトリにが含まれていない `config.php` ファイル。

**Adobe Commerce ソフトウェアをアンインストールするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. 設定ファイルを削除します。
   - Adobe Commerce 2.2 以降：

     ```bash
     rm app/etc/config.php
     ```

   - Adobe Commerce 2.1 の場合：

     ```bash
     rm app/etc/config.local.php
     ```

1. Adobe Commerce アプリケーションをアンインストールします。

   ```bash
   php bin/magento setup:uninstall -n
   ```

1. Adobe Commerceが正常にアンインストールされたことを確認します。

   次のメッセージが表示され、アンインストールが正常に完了したことを確認します。

   ```terminal
   [SUCCESS]: Magento uninstallation complete.
   ```

1. をクリア `var/` サブディレクトリ。

   ```bash
   rm -rf var/*
   ```

1. ログアウトします。

>[!TIP]
>
>オプションで、ビルドのキャッシュをクリーンアップすることをお勧めします。
>
>```bash
>magento-cloud project:clear-build-cache
>```

## 再デプロイメントを強制的に実行

Adobe Commerceをアンインストールしようとしてもデプロイメントが引き続き失敗する場合は、手動で強制的に再デプロイメントしてみてください。

```bash
git commit --allow-empty -m "<message>" && git push <origin> <branch>
```

## データベースをリセット

Adobe Commerceをアンインストールしようとして、コマンドが失敗または完了しなかった場合は、手動でデータベースをリセットできます。

**データベースをリセットするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。

1. SSH を使用してリモート環境にログインします。

   ```bash
   magento-cloud ssh
   ```

1. データベースに接続します。

   ```bash
   mysql -h database.internal
   ```

1. をドロップ `main` データベース。

   ```shell
   drop database main;
   ```

1. 空のを作成 `main` データベース。

   ```shell
   create database main;
   ```

1. 次の設定ファイルを削除します。

   - `config.php`
   - `config.php.bak`
   - `env.php`
   - `env.php.bak`

1. ログアウトして、再デプロイをトリガーします。

   ```bash
   magento-cloud environment:redeploy
   ```

{{redeploy-warning}}
