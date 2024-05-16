---
title: シナリオベースのデプロイメント
description: カスタム設定ファイルを使用して、クラウドインフラストラクチャデプロイメント上でAdobe Commerceをカスタマイズする方法について説明します。
feature: Cloud, Configuration, Deploy, Build
exl-id: df243068-c7a3-46dd-abe9-9e05198fa343
source-git-commit: 9b3772cf640ebc56063434e1aa8acb1ec51dc63c
workflow-type: tm+mt
source-wordcount: '808'
ht-degree: 0%

---

# シナリオベースのデプロイメント

（を使用） `ece-tools` 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、デフォルトのデプロイメント動作をカスタマイズできます。
この機能では、を使用します **シナリオ** および **手順** 設定で以下を行います。

- **シナリオ設定** – 各デプロイメント フックは *シナリオ*&#x200B;これは、デプロイメントタスクを完了するためのシーケンスパラメーターと設定パラメーターを記述する XML 設定ファイルです。 シナリオは次で設定します `hooks` の節 `.magento.app.yaml` ファイル。

- **手順の設定** – 各シナリオでは、次のシーケンスが使用されます *手順* これは、デプロイメントタスクの完了に必要な操作をプログラムで記述するものです。 この手順は XML ベースのシナリオ設定ファイルで設定します。

クラウドインフラストラクチャー上のAdobe Commerceは、以下のセットを提供します [デフォルトのシナリオ](https://github.com/magento/ece-tools/tree/2002.1/scenario) および [デフォルトの手順](https://github.com/magento/ece-tools/tree/2002.1/src/Step) が含まれる `ece-tools` パッケージ。 デフォルトの設定をオーバーライドまたはカスタマイズするカスタム XML 設定ファイルを作成することで、デプロイメントの動作をカスタマイズできます。 シナリオと手順を使用して、カスタムモジュールからコードを実行することもできます。

## ビルドフックとデプロイフックを使用したシナリオの追加

Adobe Commerceを構築およびデプロイするためのシナリオをに追加します `hooks` の節 `.magento.app.yaml` ファイル。 各フックは、各フェーズで実行するシナリオを指定します。 次の例に、デフォルトのシナリオ設定を示します。

> `magento.app.yaml` フック

```yaml
hooks:
    build: |
        set -e
        php ./vendor/bin/ece-tools run scenario/build/generate.xml
        php ./vendor/bin/ece-tools run scenario/build/transfer.xml
    deploy: |
        php ./vendor/bin/ece-tools run scenario/deploy.xml
    post_deploy: |
        php ./vendor/bin/ece-tools run scenario/post-deploy.xml
```

>[!NOTE]
>
>～の放出に伴って `ece-tools` 2002.1.x、新しい [フック設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/app/properties/hooks-property.html) 形式。 の従来の形式 `ece-tools` 2002.0.x リリースは引き続きサポートされます。 ただし、シナリオベースのデプロイメント機能を使用するには、新しい形式に更新する必要があります。

## シナリオ手順のレビュー

フック設定では、各シナリオは、ビルド、デプロイまたはデプロイ後のタスクを実行する手順を含む XML ファイルです。 例： `scenario/transfer` ファイルのステップは次の 3 つです。 `compress-static-content`, `clear-init-directory`、および `backup-data`

> `scenario/transfer.xml`

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
    <step name="compress-static-content" type="Magento\MagentoCloud\Step\Build\CompressStaticContent" priority="100"/>
    <step name="clear-init-directory" type="Magento\MagentoCloud\Step\Build\ClearInitDirectory" priority="200"/>
    <step name="backup-data" type="Magento\MagentoCloud\Step\Build\BackupData" priority="300">
        <arguments>
            <argument name="logger" xsi:type="object">Psr\Log\LoggerInterface</argument>
            <argument name="steps" xsi:type="array">
                <item name="static-content" xsi:type="object" priority="100">Magento\MagentoCloud\Step\Build\BackupData\StaticContent</item>
                <item name="writable-dirs" xsi:type="object"  priority="200">Magento\MagentoCloud\Step\Build\BackupData\WritableDirectories</item>
            </argument>
        </arguments>
    </step>
</scenario>
```

## デフォルトシナリオの拡張

次の例では、フック設定にデプロイ設定ファイルを追加することで、デフォルトのデプロイシナリオを拡張しています。

```yaml
hooks:
  deploy: |
    php ./vendor/bin/ece-tools run scenario/deploy.xml vendor/<vendor-name>/<module-name>/deploy.xml vendor/<vendor-name>/<module-name>/deploy2.xml
```

デプロイメント時に、カスタムシナリオは次のルールに基づいてデフォルトのシナリオと結合されます。

- シナリオの優先順位は、フック定義内のシーケンスに基づいて設定され、リストされた最後のシナリオが最も優先順位が高くなります。

  この例では、シナリオの優先度は次のとおりです。

   1. `vendor/vendor-name/module-name/deploy2.xml`
   1. `vendor/vendor-name/module-name/deploy.xml`
   1. `scenario/deploy.xml` （デフォルトまたはベースラインシナリオ）

- 最も優先度が高いシナリオのステップは、他のシナリオの同じ名前のステップを上書きします。 新しい手順が設定に追加されます。 例えば（C → B → A）のように、各シナリオが右から左に優先順位付けされる 2 つ以上のシナリオにも同じルールが適用されます。

### デフォルトの手順を削除

デフォルトのシナリオからステップを削除するには、 `skip` パラメーター。

例えば、 `enable-maintenance-mode` および `set-production-mode` 手順デフォルトのデプロイシナリオでは、次の設定を含む設定ファイルを作成します。

> `vendor/vendor-name/module-name/deploy-custom-mode-config.xml`

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
    <step name="enable-maintenance-mode" skip="true" />
    <step name="set-production-mode"  skip="true" />
</scenario>
```

カスタム設定ファイルを使用するには、デフォルトのを更新します `.magento.app.yaml` ファイル。

> `.magento.app.yaml` カスタムデプロイメントシナリオの場合

```yaml
hooks:
    build: |
        set -e
        php ./vendor/bin/ece-tools run scenario/build/generate.xml
        php ./vendor/bin/ece-tools run scenario/build/transfer.xml
    deploy: |
        php ./vendor/bin/ece-tools run scenario/deploy.xml vendor/vendor-name/module-name/deploy-custom-mode-config.xml
    post_deploy: |
        php ./vendor/bin/ece-tools run scenario/post-deploy.xml
```

### デフォルトの手順を置換

カスタムシナリオは、デフォルトの手順を置き換えて、カスタムの実装を提供できます。 それには、デフォルトのステップ名をカスタムステップの名前として使用します。

例： [デフォルトのデプロイシナリオ] この `enable-maintenance-mode` デフォルトで実行されるステップ [EnableMaintenanceMode PHP スクリプト].

```xml
<step name="enable-maintenance-mode" type="Magento\MagentoCloud\Step\EnableMaintenanceMode" priority="300"/>
```

この手順を上書きするには、次の場合に別のスクリプトを実行するようにカスタムシナリオ設定ファイルを作成します `enable-maintenance-mode` ステップが実行されます。

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
<scenario>
    <step name="enable-maintenance-mode" type="VendorName\VendorModule\Step\CustomEnableMaintenanceMode" priority="300"/>
</scenario>
```

### ステップの優先度の変更

カスタムシナリオで、デフォルトのステップの優先度を変更できます。 次の手順で、の優先度を変更します `enable-maintenance-mode` ステップ開始 `300` 対象： `10` そのため、デプロイシナリオの前半でステップが実行されます。

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
<scenario>
    <step name="enable-maintenance-mode" type="Magento\MagentoCloud\Step\EnableMaintenanceMode" priority="10"/>
</scenario>
```

この例では、 `enable-maintenance-mode` デフォルトのデプロイメントシナリオでは他のすべてのステップよりも優先度が低いので、ステップがシナリオの最初に移動します。

### 例：デプロイシナリオの拡張

次の例では、をカスタマイズしています [デフォルトのデプロイシナリオ] （次の変更を含む）。

- を置き換えます `remove-deploy-failed-flag` カスタムステップを含むステップ
- をスキップします `clean-redis-cache` デプロイ前手順のサブステップ
- をスキップします `unlock-cron-jobs` ステップ
- をスキップします `validate-config` 重要なバリデーターを無効にする手順
- 新しいプレデプロイ手順を追加します

> `vendor/vendor-name/module-name/deploy-extended.xml`

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
    <!-- Replace "remove-deploy-failed-flag" step with custom step -->
    <step name="remove-deploy-failed-flag" type="Vendor\ModuleName\Step\Deploy\RemoveDeployFailedFlag" priority="100"/>

    <!-- Skip "clean-redis-cache" sub-step in pre-deploy step -->
    <step name="pre-deploy" type="Magento\MagentoCloud\Step\Deploy\PreDeploy" priority="200">
        <arguments>
            <argument name="logger" xsi:type="object">Psr\Log\LoggerInterface</argument>
            <argument name="steps" xsi:type="array">
                <item name="clean-redis-cache" xsi:type="object" skip="true"/>
            </argument>
        </arguments>
    </step>

    <!-- Skip step "unlock-cron-jobs" -->
    <step name="unlock-cron-jobs" skip="true"/>

    <!-- Skip critical validators -->
    <step name="validate-config" type="Magento\MagentoCloud\Step\ValidateConfiguration" priority="300">
        <arguments>
            <argument name="logger" xsi:type="object">Psr\Log\LoggerInterface</argument>
            <argument name="validators" xsi:type="array">
                <item name="critical" xsi:type="array">
                    <item name="database-configuration" xsi:type="object" skip="true"/>
                    <item name="search-configuration" xsi:type="object" skip="true"/>
                </item>
            </argument>
        </arguments>
    </step>

    <!-- Add new step into the beginning of the deploy scenario -->
    <step name="new-pre-deploy-step" type="Vendor\ModuleName\Step\Deploy\PreDeploy" priority="10"/>
</scenario>
```

このスクリプトをプロジェクトで使用するには、次の設定をに追加します。 `.magento.app.yaml` クラウドインフラストラクチャプロジェクト上のAdobe Commerce用ファイル：

```yaml
hooks:
    build: |
        set -e
        php ./vendor/bin/ece-tools run scenario/build/generate.xml
        php ./vendor/bin/ece-tools run scenario/build/transfer.xml
    deploy: |
        php ./vendor/bin/ece-tools run scenario/deploy.xml vendor/vendor-name/module-name/deploy-extended.xml
    post_deploy: |
        php ./vendor/bin/ece-tools run scenario/post-deploy.xml
```

>[!TIP]
>
>確認できます [デフォルトのシナリオ](https://github.com/magento/ece-tools/tree/2002.1/scenario) および [デフォルトのステップ設定](https://github.com/magento/ece-tools/tree/2002.1/src/Step) が含まれる `ece-tools` GitHub リポジトリ：プロジェクトのビルド、デプロイ、デプロイ後のタスクに合わせてカスタマイズするシナリオと手順を決定します。

## 拡張するカスタムモジュールの追加 `ece-tools`

この `ece-tools` パッケージは、セマンティックバージョン標準に準拠したデフォルトの API インターフェイスを提供します。 すべての API インターフェイスにはと示されています。 **@api** 注釈。 カスタムモジュールを作成し、必要に応じてデフォルトコードを変更することで、デフォルトの API 実装を独自の API 実装に置き換えることができます。

クラウドインフラストラクチャー上のAdobe Commerceでカスタムモジュールを使用するには、 `ece-tools` パッケージ。 登録プロセスは、Adobe Commerceへのモジュール登録に使用するプロセスと似ています。

**にモジュールを登録するには `ece-tools` package**:

1. を作成または拡張する `registration.php` ファイルはモジュールのルートにあります。

   ```php?start_inline=1
   \Magento\MagentoCloud\ExtensionRegistrar::register('module-name', __DIR__);
   ```

1. を更新 `autoload` モジュール設定ファイルに含めるセクション `registration.php` モジュール ファイルを自動ロードするファイル `composer.json`.

   ```json
   {
     "name": "vendor/ece-tools-extend",
     "description": "Extension for ece-tools",
     "type": "magento2-component",
     "version": "1.0.0",
     "license": "OSL-3.0",
     "autoload": {
       "files": [ "registration.php" ],
       "psr-4": {
         "Vendor\\EceToolExtend\\": "src/"
       }
     }
   }
   ```

1. を追加 `config/services.xml` ファイルをモジュールに追加します。 この設定はに結合されます `config/services.xml` から `ece-tools` パッケージ。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <container xmlns="http://symfony.com/schema/dic/services"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://symfony.com/schema/dic/services https://symfony.com/schema/dic/services/services-1.0.xsd">
       <services>
           <defaults autowire="true" autoconfigure="true" public="true"/>
   
           <prototype namespace="Vendor\EceToolExtend\" resource="../src/*" exclude="../src/{Test}"/>
   
           <!-- Use your own implementation of EnvironmentDataInterface -->
           <service id="Magento\MagentoCloud\Config\EnvironmentDataInterface" alias="Vendor\EceToolExtend\Config\CustomEnvironmentData" />
       </services>
   </container>
   ```

依存関係の挿入の詳細については、を参照してください。 [共存注射](https://symfony.com/doc/current/components/dependency_injection.html).

<!-- link definitions -->

[デフォルトのデプロイシナリオ]: https://github.com/magento/ece-tools/blob/develop/scenario/deploy.xml
[EnableMaintenanceMode PHP スクリプト]: https://github.com/magento/ece-tools/blob/develop/src/Step/EnableMaintenanceMode.php
