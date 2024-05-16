---
title: 変数のレベルとオプション
description: クラウドインフラストラクチャプロジェクトのランタイム環境でAdobe Commerceをカスタマイズする際に使用する、様々な変数レベルおよびオプションについて説明します。
feature: Cloud, Configuration, Security
exl-id: 11aa0862-94c0-47fb-946a-0148f75cc24c
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '446'
ht-degree: 0%

---

# 変数レベル

プロジェクト変数は、プロジェクト内のすべての環境に適用されます。 環境変数は、特定の環境またはブランチに適用されます。 環境 _継承_ 親環境からの変数定義。

継承された値を上書きするには、環境用に変数を特別に定義します。 例えば、開発用の変数を設定するには、にある変数値を定義します。 `.magento.env.yaml` 統合環境のファイル。 統合環境から分岐するすべての環境は、これらの値を継承します。 参照： [デプロイメント設定](configure-env-yaml.md) を使用した環境の設定について詳しくは、 `.magento.env.yaml` ファイル。

>[!BEGINTABS]

>[!TAB CLI]

**Cloud CLI を使用して変数を設定するには**:

- **プロジェクト固有の変数** – に同じ値を設定する _all_ プロジェクト内の環境。 これらの変数は、ビルド時と実行時にすべての環境で使用できます。

  ```bash
  magento-cloud variable:create --level project --name <variable-name> --value <variable-value>
  ```

- **環境固有の変数** – に一意の値を設定する _特定_ 環境。 これらの変数は、実行時に使用でき、子環境に継承されます。 を使用して、コマンドで環境を指定します。 `-e` オプション。

  ```bash
  magento-cloud variable:create --level environment --name <variable-name> --value <variable-value>
  ```

プロジェクト固有の変数を設定した後、変更を有効にするには、リモート環境を手動で再デプロイする必要があります。 新しいコミットをプッシュして、再デプロイメントをトリガーにします。

>[!TAB コンソール]

**を使用して変数を設定するには[!DNL Cloud Console]**:

1. が含まれる _[!DNL Cloud Console]_を開き、プロジェクトのナビゲーションの右側にある「設定」アイコンをクリックします。

   ![プロジェクトの設定](../../assets/icon-configure.png){width="36"}

1. プロジェクトレベルの変数を設定するには、の下に _プロジェクト設定_ click **変数**.

   ![プロジェクト変数](../../assets/ui-project-variables.png)

1. 環境レベルの変数を設定するには、 _環境_ リストで環境を選択して、 **[!UICONTROL Variables]** タブ。

   ![「環境変数」タブ](../../assets/ui-environment-variables.png)

1. クリック **[!UICONTROL Create variable]**.

1. 変数の名前と値を指定します。 次のオプションから選択します。

   - 実行時に使用可能
   - ビルド時に使用可能
   - JSON 値
   - 機密変数（コンソールおよび CLI 応答では非表示の値）
   - 継承可能にする（子環境は環境レベルの変数を継承できます）

1. クリック **[!UICONTROL Create variable]**.

>[!CAUTION]
>
>での環境固有の変数の設定 [!DNL Cloud Console] 環境を自動的に再デプロイします。

>[!ENDTABS]

## 可視性

を使用して、ビルド時または実行時の変数の表示を制限できます。 `--visible-<build|runtime>` コマンド。 また、継承と機密性を設定するオプションもあります。

変数が表示または継承されないようにするには、次のオプションを使用します。

- `--inheritable false` – 子環境の継承を無効にします。 これは、で実稼動のみの値を設定する場合に便利です `master` 分岐し、他のすべての環境が同じ名前のプロジェクトレベルの変数を使用できるようにします。
- `--sensitive true` – 変数を以下のようにマークする _読み取れない_ が含まれる [!DNL Cloud Console]. ユーザーインターフェイスでは変数を表示できませんが、他の変数と同様に、アプリケーションコンテナから変数を表示できます。

以下は、変数が表示または継承されるのを防ぐ具体的な例を示しています。 CLI では、以下のオプションのみを指定できます。 このケースは、使用可能なすべての環境変数に関係しているわけではありません。

```bash
magento-cloud variable:create --name <variable-name> --value <variable-value> --inheritable false --sensitive true
```

## 変数のレベルと値の検証

既存の変数のリストは、Cloud CLI を使用して表示できます。

```bash
magento-cloud variables
```

```terminal
Variables on the project Project-Name (<project-id>), environment <environment-name>:
+----------------------------+-------------+-------------------------------------------+
| Name                       | Level       | Value                                     |
+----------------------------+-------------+-------------------------------------------+
| env:COMPOSER_AUTH          | project     | {                                         |
|                            |             |    "http-basic": {                        |
|                            |             |       "repo.magento.com": {               |
|                            |             |       "username":                         |
|                            |             | "<public-key>",                           |
|                            |             |       "password":                         |
|                            |             | "<private-key>"                           |
|                            |             |     }                                     |
|                            |             |   }                                       |
|                            |             | }                                         |
| ADMIN_EMAIL                | project     | admin@123.com                             |
| ADMIN_EMAIL                | environment | admin@123.com                             |
| ADMIN_PASSWORD             | environment | password                                  |
| ADMIN_URL                  | environment | admin123                                  |
| ADMIN_USERNAME             | environment | admin                                     |
| php:newrelic.license       | environment | xxxx71fb030366182117f955a22e4baf8exxxxxx  |
+----------------------------+-------------+-------------------------------------------+
```
