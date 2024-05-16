---
title: 開発の準備
description: 資格情報を収集し、クラウドインフラストラクチャプロジェクト上のCommerceで使用する開発ワークスペースを設定するために使用できるツールについて説明します。
recommendations: noDisplay, catalog
exl-id: 8f88161f-3580-453b-b977-2c6e3824cc02
source-git-commit: 85ff1283f773823ff2c6e6ab8f391fd5b4aa00e4
workflow-type: tm+mt
source-wordcount: '471'
ht-degree: 0%

---

# 開発の準備

Commerceを初めて使用する場合でも、クラウドインフラストラクチャに移行する既存のCommerce オーナーでも、これらの手順を使用して Cloud プロジェクトの開発ワークスペースを準備します。 これらの手順の一部を既に完了している場合、または既存のAdobe Commerce開発者環境がある場合は、期待される結果について次の点を確認し、次の手順に進みます。 一部の設定とワークフローは、一般的なオンプレミスのインストールとは異なります。

## 資格情報

ワークスペースを設定する前に、次のキーとアカウントのアクセス権を収集します。

- **認証キー（Composer キー）**

  認証キーは、Adobe Commerce Composer リポジトリへの安全なアクセスを提供する 32 文字の認証トークンです（`repo.magento.com`）を選択し、GitHub など、アプリケーション開発に必要なその他の Git サービスを選択します。 アカウントには複数の認証キーを設定できます。 ワークスペースを設定する場合は、まずコードリポジトリに固有のキーを 1 つ使用します。 キーがない場合は、プロジェクトの所有者に問い合わせるか、 [認証キー](../cloud-guide/development/authentication-keys.md) あなた自身。

- **クラウドプロジェクトアカウント**

  プロジェクトオーナーに、クラウドインフラストラクチャプロジェクトのAdobe Commerceへの招待を受ける必要があります。 電子メールによる招待を受け取ったら、リンクをクリックし、画面の指示に従ってアカウントを作成します。 参照： [オンボーディング](onboarding.md).

- **Adobe Commerce暗号化キー**

  既存のシステムのみをインポートする場合は、データベースのアクセスとデータを保護するために使用する暗号化キーを取得します。 このキーについて詳しくは、 [暗号化キーに関する問題の解決](https://experienceleague.adobe.com/docs/commerce-knowledge-base/kb/troubleshooting/miscellaneous/resolve-issues-with-encryption-key.html)

## デベロッパーツール

- **Cloud CLI のインストール**

  のインストール `magento-cloud` CLI を使用すると、クラウド環境を管理し、自動化タスクを実行できます。 参照： [クラウド CLI](../cloud-guide/dev-tools/cloud-cli-overview.md) を参照してください。

- **ローカル開発およびテスト用の Docker のインストール**

  オプションで、Docker 環境を使用してクラウドインフラストラクチャー上のCommerceをエミュレートします `integration` ローカル開発の環境。 必須のコンポーネントには、Adobe Commerce v2 テンプレート、Docker Compose および `ece-tools` パッケージ。

   - [Docker アーキテクチャと一般的なコマンド](../cloud-guide/dev-tools/cloud-docker.md)
   - [Docker 開発環境の起動](https://developer.adobe.com/commerce/cloud-tools/docker/setup/)
   - [ECE-Tools パッケージ](../cloud-guide/dev-tools/package-overview.md)

- **Git ベースのサービスの統合**

  オプションで、GitHub や GitLab などの Git ベースのホスティングサービスと、クラウドインフラストラクチャー上のAdobe Commerceを統合します。 参照： [統合](../cloud-guide/integrations/overview.md).

## プロジェクトコード

リモート環境とやり取りするには、安全な接続が不可欠です。 新規プロジェクトの場合、 [にログインします [!DNL Cloud Console]](https://console.adobecommerce.com) をクリックして、 **[!UICONTROL No SSH key]**. このアイコンはコマンドフィールドの右側にあり、プロジェクトに SSH キーが含まれていない場合に表示されます。 参照： [安全な接続](../cloud-guide/development/secure-connections.md#add-an-ssh-public-key-to-your-account).

**コードベースをローカルワークステーションに複製するには**:

1. が含まれる [[!DNL Cloud Console]](https://console.adobecommerce.com)を選択し、 **[!UICONTROL code]** を選択し、 **[!UICONTROL Git]** タブ。

   ![コードのクローン](../assets/ui-git-code.png){width="450"}

1. をコピーします `git clone ...` コマンドが提供されました。

1. ターミナルでを作成し、作業ディレクトリに変更します。

1. を貼り付けて実行 `git clone ...` コマンド。

>[!TIP]
>
>Adobeは、特定のバージョンのAdobe Commerceのパッケージ手順を含むテンプレートリポジトリを使用して、初期プロジェクト環境をプロビジョニングします。 をレビュー [プロジェクト ファイル構造](../cloud-guide/project/file-structure.md) 重要なプロジェクトファイルとクラウドテンプレートに関するトピックと詳細情報。
