---
title: プロジェクト構造
description: クラウドインフラストラクチャー上のAdobe Commerceのファイル構造およびプロジェクトテンプレートについて説明します。
exl-id: 6dc559bd-116b-4745-a85b-731508e113ff
source-git-commit: 47ef728ea46b137eeaabbdbada940143d8773ef0
workflow-type: tm+mt
source-wordcount: '454'
ht-degree: 0%

---

# プロジェクト構造

クラウドインフラストラクチャー上のAdobe Commerce プロジェクトには、資格情報とアプリケーション設定に不可欠なファイルが含まれています。 これらのファイルは、Adobe Commerceのバージョンに応じて、テンプレートとしてで使用できます。 のAdobe Commerce バージョンに基づいたクラウドテンプレートを参照してください。 [`magento/magento-cloud` GitHub リポジトリ](https://github.com/magento/magento-cloud).

次の表では、クラウドプロジェクトに含まれるファイルについて説明します。

| ファイル | 説明 |
| ------------------------- | ------------ |
| `/.magento/routes.yaml` | リダイレクトする設定ファイル `www` apex ドメインに追加し、 `php` http を提供するアプリケーション。 参照： [ルートの設定](../routes/routes-yaml.md). |
| `/.magento/services.yaml` | MySQL インスタンス（MariaDB）、Redis、OpenSearch またはElasticsearchを定義する設定ファイルです。 参照： [サービスの設定](../services/services-yaml.md). |
| `/app` | この `code` フォルダーは、カスタムモジュールに使用されます。 この `design` フォルダーの使用目的 [カスタムテーマ](../store/custom-theme.md). この `etc` フォルダーには、アプリケーションの設定ファイルが含まれます。 |
| `/m2-hotfixes` | カスタムパッチに使用します。 |
| `/update` | サポートモジュールが使用するサービスフォルダー。 |
| `.gitignore` | 無視するファイルとディレクトリを指定します。 参照： [`.gitignore` 参照](#ignoring-files). |
| `.magento.app.yaml` | アプリケーションを構築するためのプロパティを定義する設定ファイル。 参照： [アプリケーションの設定](../application/configure-app-yaml.md). |
| `.magento.env.yaml` | ビルド、デプロイ、デプロイ後のフェーズ用の設定ファイル。 この `ece-tools` パッケージには、このファイルのサンプルが含まれています。 参照： [環境の設定](../environment/configure-env-yaml.md). |
| `composer.json` | Adobe Commerceと設定スクリプトを取得して、アプリケーションを準備します。 参照： [必須パッケージ](../development/overview.md#required-packages). |
| `composer.lock` | パッケージごとにバージョンの依存関係を格納します。 参照： [必須パッケージ](../development/overview.md#required-packages). |
| `magento-vars.php` | の定義に使用 [複数のストア](../store/multiple-sites.md) および変数を使用したサイト |

{style="table-layout:auto"}

>[!NOTE]
>
>ローカルの変更をリモートサーバーにプッシュする場合、デプロイスクリプトはの設定ファイルで定義されている値を使用します。 `.magento` 次に、スクリプトはディレクトリとその内容を削除します。 ローカル開発環境は影響を受けません。

## アプリケーションのルートディレクトリ

アプリケーションのルートディレクトリの場所は、環境によって異なります。

- **スターターと Pro の統合**: `/app`
- **スターター実稼動**: `/<project-ID>`
- **Pro ステージング**: `/<project-ID>_stg`
- **実稼動環境に対応**: `/<project-ID>`

### 書き込み可能ディレクトリ

リモート統合環境、ステージング環境、実稼動環境は読み取り専用です。 次のディレクトリは、 *のみ* 書き込み可能なディレクトリ （セキュリティ上の理由）:

- `var`
- `pub/static`
- `pub/media`
- `app/etc`
- `/tmp`

>[!NOTE]
>
>実稼動環境とステージング環境では、3 ノードクラスター内の各ノードには次の機能があります `/tmp` 他のノードと共有されていないディレクトリ。

## ファイルを無視

基地がある `.gitignore` Adobe Commerce on cloud infrastructure プロジェクトリポジトリを含んだファイル。 最新のを表示 [magento-cloud リポジトリの.gitignore ファイル](https://github.com/magento/magento-cloud/blob/master/.gitignore). に含まれるファイルを追加するには `.gitignore` リストから、を使用できます。 `-f` コミットをステージングする場合の「強制」オプション：

```bash
git add <path/filename> -f
```

## 基本テンプレートの変更

次の手順を使用して、既存のプロジェクトの構造を変更し、クラウドインフラストラクチャー上のAdobe Commerceの最新の基本テンプレートを反映させることができます。

1. プロジェクトをローカルワークステーションにクローンします。

1. を更新 `composer.json` ファイルにの次の値を設定します `extra` セクション。

   ```json
   "extra": {
       "magento-force": true
       "magento-deploystrategy": "copy"
   }
   ```

1. を追加 `.gitignore` 基本テンプレート用に設計されたファイル。 例えば、 `.gitignore` バージョン 2.2.6 テンプレート用のファイルには、 [2.2.6 の.gitignore](https://github.com/magento/magento-cloud/blob/2.2.6/.gitignore) ファイルを参照として使用します。

1. Git キャッシュをクリアします。

   ```bash
   git rm -r --cached .
   ```

1. 変更を追加してコミットします。

   ```bash
   git add -A && git commit -m "Update base template"
   ```

{{redeploy-warning}}
