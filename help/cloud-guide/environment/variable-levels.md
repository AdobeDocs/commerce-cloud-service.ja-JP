---
title: 変数のレベルとオプション
description: クラウドインフラストラクチャプロジェクトのランタイム環境でのAdobe Commerceのカスタマイズで使用される様々な変数レベルとオプションについて説明します。
feature: Cloud, Configuration, Security
exl-id: 11aa0862-94c0-47fb-946a-0148f75cc24c
source-git-commit: 13e76d3e9829155995acbb72d947be3041579298
workflow-type: tm+mt
source-wordcount: '446'
ht-degree: 0%

---

# 変数レベル

プロジェクト変数は、プロジェクト内のすべての環境に適用されます。 環境変数は、特定の環境またはブランチに適用されます。 環境 _継承_ 変数の定義を親環境から取得します。

継承された値は、環境専用の変数を定義することで上書きできます。 例えば、開発用の変数を設定するには、 `.magento.env.yaml` ファイルを統合環境に保存します。 統合環境から分岐したすべての環境は、これらの値を継承します。 詳しくは、 [デプロイメント設定](configure-env-yaml.md) を使用した環境の設定の詳細 `.magento.env.yaml` ファイル。

>[!BEGINTABS]

>[!TAB CLI]

**Cloud CLI を使用して変数を設定するには**:

- **プロジェクト固有の変数** — に同じ値を設定します。 _すべて_ 環境を作成します。 これらの変数は、すべての環境で、ビルド時および実行時に使用できます。

  ```bash
  magento-cloud variable:create --level project --name <variable-name> --value <variable-value>
  ```

- **環境固有の変数** — 一意の値を _特定の_ 環境。 これらの変数は実行時に使用でき、子環境に継承されます。 次のコマンドを使用して、コマンドで環境を指定します。 `-e` オプション。

  ```bash
  magento-cloud variable:create --level environment --name <variable-name> --value <variable-value>
  ```

プロジェクト固有の変数を設定した後、変更を有効にするには、リモート環境を手動で再デプロイする必要があります。 新しいコミットをプッシュして、再デプロイメントをトリガー化します。

>[!TAB コンソール]

**を使用して変数を設定するには[!DNL Cloud Console]**:

1. Adobe Analytics の _[!DNL Cloud Console]_で、プロジェクトナビゲーションの右側にある設定アイコンをクリックします。

   ![プロジェクトを設定](../../assets/icon-configure.png){width="36"}

1. プロジェクトレベルの変数を設定するには、次の手順に従います。 _プロジェクト設定_ click **変数**.

   ![プロジェクト変数](../../assets/ui-project-variables.png)

1. 環境レベルの変数を設定するには、 _環境_ リストで、環境を選択し、 **[!UICONTROL Variables]** タブをクリックします。

   ![「環境変数」タブ](../../assets/ui-environment-variables.png)

1. クリック **[!UICONTROL Create variable]**.

1. 変数の名前と値を指定します。 次のオプションから選択します。

   - 実行時に使用可能
   - ビルド時に使用可能
   - JSON 値
   - 機密変数（コンソールおよび CLI 応答で非表示の値）
   - 継承可能にする（子環境は環境レベルの変数を継承できます）

1. クリック **[!UICONTROL Create variable]**.

>[!CAUTION]
>
>での環境固有の変数の設定 [!DNL Cloud Console] 環境を自動的に再デプロイします。

>[!ENDTABS]

## 表示

を使用して、ビルド中または実行時に変数の表示を制限できます。 `--visible-<build|runtime>` コマンドを使用します。 また、継承と感度を設定するオプションもあります。

変数が表示または継承されないようにするには、次のオプションを使用します。

- `--inheritable false` — 子環境の継承を無効にします。 これは、 `master` ブランチを作成し、他のすべての環境で同じ名前のプロジェクトレベルの変数を使用できるようにします。
- `--sensitive true` — 変数を _読み取り不可能_ （内） [!DNL Cloud Console]. 変数は、ユーザーインターフェイスでは表示できません。ただし、他の変数と同様に、アプリケーションコンテナから変数を表示できます。

次の例は、変数が表示または継承されないようにする具体的な例を示しています。 CLI では、これらのオプションのみ指定できます。 この場合、使用可能なすべての環境変数に関係するわけではありません。

```bash
magento-cloud variable:create --name <variable-name> --value <variable-value> --inheritable false --sensitive true
```

## 変数のレベルと値の検証

Cloud CLI を使用して、既存の変数のリストを表示できます。

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
