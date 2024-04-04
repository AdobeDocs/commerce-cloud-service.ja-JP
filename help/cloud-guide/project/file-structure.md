---
title: プロジェクト構造
description: クラウドインフラストラクチャ上のAdobe Commerceのファイル構造とプロジェクトテンプレートについて説明します。
exl-id: 6dc559bd-116b-4745-a85b-731508e113ff
source-git-commit: 47ef728ea46b137eeaabbdbada940143d8773ef0
workflow-type: tm+mt
source-wordcount: '454'
ht-degree: 0%

---

# プロジェクト構造

Adobe Commerce on cloud infrastructure プロジェクトには、資格情報とアプリケーション設定に必須のファイルが含まれています。 これらのファイルは、Adobe Commerceのバージョンに応じて、テンプレートとしてで使用できます。 詳しくは、 [`magento/magento-cloud` GitHub リポジトリ](https://github.com/magento/magento-cloud).

次の表に、クラウドプロジェクトに含まれるファイルを示します。

| ファイル | 説明 |
| ------------------------- | ------------ |
| `/.magento/routes.yaml` | リダイレクトする設定ファイル `www` apex ドメインに `php` HTTP を提供するアプリケーション。 詳しくは、 [ルートの設定](../routes/routes-yaml.md). |
| `/.magento/services.yaml` | MySQL インスタンス (MariaDB)、Redis、OpenSearch またはElasticsearchを定義する設定ファイル。 詳しくは、 [サービスの設定](../services/services-yaml.md). |
| `/app` | The `code` フォルダーは、カスタムモジュールに使用されます。 The `design` 次のフォルダーが使用されています： [カスタムテーマ](../store/custom-theme.md). The `etc` フォルダーには、アプリケーションの設定ファイルが含まれます。 |
| `/m2-hotfixes` | カスタムパッチに使用します。 |
| `/update` | サポートモジュールで使用されるサービスフォルダーです。 |
| `.gitignore` | 無視するファイルとディレクトリを指定します。 詳しくは、 [`.gitignore` 参照](#ignoring-files). |
| `.magento.app.yaml` | アプリケーションを構築するためのプロパティを定義する設定ファイル。 詳しくは、 [アプリケーションの設定](../application/configure-app-yaml.md). |
| `.magento.env.yaml` | ビルド、デプロイ、およびデプロイ後の各フェーズの設定ファイル。 The `ece-tools` パッケージには、このファイルのサンプルが含まれています。 詳しくは、 [環境の設定](../environment/configure-env-yaml.md). |
| `composer.json` | Adobe Commerceと設定スクリプトを取得し、アプリケーションを準備します。 詳しくは、 [必要なパッケージ](../development/overview.md#required-packages). |
| `composer.lock` | すべてのパッケージのバージョンの依存関係を格納します。 詳しくは、 [必要なパッケージ](../development/overview.md#required-packages). |
| `magento-vars.php` | 定義に使用 [複数のストア](../store/multiple-sites.md) 変数を使用するサイトとサイト。 |

{style="table-layout:auto"}

>[!NOTE]
>
>ローカルの変更をリモートサーバーにプッシュすると、デプロイスクリプトは、 `.magento` ディレクトリに移動した後、スクリプトはディレクトリとその内容を削除します。 お使いのローカル開発環境は影響を受けません。

## アプリケーションのルートディレクトリ

アプリケーションのルートディレクトリの場所は、環境によって異なります。

- **スターターと Pro の統合**: `/app`
- **スターター実稼動**: `/<project-ID>`
- **Pro Staging**: `/<project-ID>_stg`
- **Pro Production**: `/<project-ID>`

### 書き込み可能なディレクトリ

リモートの統合環境、ステージング環境、実稼動環境は読み取り専用です。 次のディレクトリは *のみ* セキュリティ上の理由から書き込み可能なディレクトリ：

- `var`
- `pub/static`
- `pub/media`
- `app/etc`
- `/tmp`

>[!NOTE]
>
>実稼動環境とステージング環境では、3 ノードクラスター内の各ノードに `/tmp` 他のノードと共有されていないディレクトリ。

## ファイルを無視

基地がある `.gitignore` ファイルをクラウドインフラストラクチャのAdobe Commerceプロジェクトリポジトリに保存します。 最新の [magento-cloud リポジトリの.gitignore ファイル](https://github.com/magento/magento-cloud/blob/master/.gitignore). にあるファイルを追加するには、以下を実行します。 `.gitignore` リストに含まれる値は、 `-f` (force) オプション：コミットをステージングする場合：

```bash
git add <path/filename> -f
```

## 基本テンプレートを変更

次の手順を使用して、クラウドインフラストラクチャ上のAdobe Commerceの最新のベーステンプレートを反映するように既存のプロジェクトの構造を変更できます。

1. プロジェクトをローカルワークステーションに複製します。

1. を更新します。 `composer.json` ファイルの `extra` 」セクションに入力します。

   ```json
   "extra": {
       "magento-force": true
       "magento-deploystrategy": "copy"
   }
   ```

1. 次を追加： `.gitignore` 基本テンプレート用に設計されたファイル。 例えば、 `.gitignore` バージョン 2.2.6 テンプレート用のファイルで、 [.gitignore（2.2.6 用）](https://github.com/magento/magento-cloud/blob/2.2.6/.gitignore) ファイルを参照として使用します。

1. Git キャッシュをクリアします。

   ```bash
   git rm -r --cached .
   ```

1. 変更を追加してコミットします。

   ```bash
   git add -A && git commit -m "Update base template"
   ```

{{redeploy-warning}}
