---
title: 開発の準備
description: 資格情報を収集し、クラウドインフラストラクチャプロジェクト上のコマースで使用する開発ワークスペースを設定するために使用できるツールについて説明します。
recommendations: noDisplay, catalog
exl-id: 8f88161f-3580-453b-b977-2c6e3824cc02
source-git-commit: 85ff1283f773823ff2c6e6ab8f391fd5b4aa00e4
workflow-type: tm+mt
source-wordcount: '471'
ht-degree: 0%

---

# 開発の準備

Commerce を初めて使用する場合と、クラウドインフラストラクチャに移行する既存のコマース所有者の場合とでは、これらの手順を使用して、Cloud プロジェクト用の開発ワークスペースを準備します。 これらの手順の一部を既に完了している場合、または既存のAdobe Commerce開発者環境がある場合は、期待される結果について次の点を確認し、次の手順に進みます。 一部の設定とワークフローは、一般的なオンプレミスインストールとは異なります。

## 資格情報

ワークスペースを設定する前に、次のキーとアカウントアクセス権を収集します。

- **認証キー（Composer のキー）**

  認証キーは、Adobe Commerce Composer リポジトリ (`repo.magento.com`) や、アプリケーション開発に必要な GitHub などの Git サービスです。 アカウントは複数の認証キーを持つことができます。 ワークスペース設定の場合は、コードリポジトリの特定のキーから開始します。 キーがない場合は、プロジェクトの所有者に問い合わせるか、 [認証キー](../cloud-guide/development/authentication-keys.md) 自分で。

- **Cloud Project アカウント**

  プロジェクト所有者が、Adobe Commerce on cloud infrastructure プロジェクトに招待します。 電子メールの招待状を受け取ったら、リンクをクリックし、画面の指示に従ってアカウントを作成します。 詳しくは、 [オンボーディング](onboarding.md).

- **Adobe Commerce Encryption Key**

  既存のシステムのみをインポートする場合は、データベースのアクセスとデータを保護するために使用する暗号化キーを取り込みます。 このキーについて詳しくは、 [暗号化キーに関する問題を解決する](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/resolve-issues-with-encryption-key.html)

## 開発者ツール

- **Cloud CLI のインストール**

  をインストールします。 `magento-cloud` クラウド環境を管理し、自動化タスクを実行できる CLI。 詳しくは、 [Cloud CLI](../cloud-guide/dev-tools/cloud-cli-overview.md) を参照してください。

- **ローカル開発とテスト用の Docker のインストール**

  必要に応じて、Docker 環境を使用してクラウドインフラストラクチャ上のコマースをエミュレートします。 `integration` 環境のローカル開発。 Adobe Commerce v2 テンプレート、Docker Compose、 `ece-tools` パッケージ。

   - [Docker のアーキテクチャと一般的なコマンド](../cloud-guide/dev-tools/cloud-docker.md)
   - [Docker 開発環境を起動する](https://developer.adobe.com/commerce/cloud-tools/docker/setup/)
   - [ECE-Tools パッケージ](../cloud-guide/dev-tools/package-overview.md)

- **Git ベースのサービスの統合**

  オプションで、GitHub や GitLab などの Git ベースのホスティングサービスを、クラウドインフラストラクチャ上のAdobe Commerceと統合します。 詳しくは、 [統合](../cloud-guide/integrations/overview.md).

## プロジェクトコード

リモート環境との対話には、セキュアな接続が不可欠です。 新しいプロジェクトの場合、 [にログインする [!DNL Cloud Console]](https://console.adobecommerce.com) をクリックします。 **[!UICONTROL No SSH key]**. このアイコンは、コマンドフィールドの右側にあり、プロジェクトに SSH キーが含まれていない場合に表示されます。 詳しくは、 [接続の保護](../cloud-guide/development/secure-connections.md#add-an-ssh-public-key-to-your-account).

**ローカルワークステーションにコードベースを複製するには**:

1. Adobe Analytics の [[!DNL Cloud Console]](https://console.adobecommerce.com)をクリックし、 **[!UICONTROL code]** をクリックし、 **[!UICONTROL Git]** タブをクリックします。

   ![コードを複製](../assets/ui-git-code.png){width="450"}

1. をコピーします。 `git clone ...` コマンドが提供されました。

1. ターミナルで、を作成し、作業ディレクトリに変更します。

1. を貼り付けて実行します。 `git clone ...` コマンドを使用します。

>[!TIP]
>
>Adobeは、Adobe Commerceの特定のバージョン用のパッケージ手順を含むテンプレートリポジトリを使用して、初期プロジェクト環境をプロビジョニングします。 以下を確認します。 [プロジェクトファイル構造](../cloud-guide/project/file-structure.md) 重要なプロジェクトファイルとクラウドテンプレートの詳細について説明します。
