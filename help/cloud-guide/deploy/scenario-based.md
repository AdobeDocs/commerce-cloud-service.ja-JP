---
title: シナリオベースのデプロイメント
description: カスタム設定ファイルを使用して、クラウドインフラストラクチャのデプロイメント上でAdobe Commerceをカスタマイズする方法について説明します。
feature: Cloud, Configuration, Deploy, Build
exl-id: df243068-c7a3-46dd-abe9-9e05198fa343
source-git-commit: 9b3772cf640ebc56063434e1aa8acb1ec51dc63c
workflow-type: tm+mt
source-wordcount: '808'
ht-degree: 0%

---

# シナリオベースのデプロイメント

を使用 `ece-tools` 2002.1.0 以降では、シナリオベースのデプロイメント機能を使用して、デフォルトのデプロイメント動作をカスタマイズできます。
この機能は、 **シナリオ** および **手順** 設定で次の手順を実行します。

- **シナリオの設定** — 各デプロイメントフックは、 *シナリオ*：デプロイメントタスクを完了するためのシーケンスおよび設定パラメーターについて説明する XML 設定ファイル。 シナリオは、 `hooks` のセクション `.magento.app.yaml` ファイル。

- **ステップの設定** — 各シナリオは、 *手順* これは、デプロイメントタスクの完了に必要な操作をプログラムで記述するものです。 手順は、XML ベースのシナリオ設定ファイルで設定します。

Adobe Commerce on cloud infrastructure は、 [デフォルトのシナリオ](https://github.com/magento/ece-tools/tree/2002.1/scenario) および [デフォルトの手順](https://github.com/magento/ece-tools/tree/2002.1/src/Step) （内） `ece-tools` パッケージ。 カスタム XML 設定ファイルを作成してデフォルトの設定を上書きまたはカスタマイズすることで、デプロイメント動作をカスタマイズできます。 また、シナリオと手順を使用して、カスタムモジュールからコードを実行することもできます。

## ビルドフックとデプロイフックを使用したシナリオの追加

Adobe Commerceを構築してデプロイするシナリオを `hooks` のセクション `.magento.app.yaml` ファイル。 各フックは、各フェーズで実行するシナリオを指定します。 次の例は、デフォルトのシナリオ設定を示しています。

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
>のリリースに伴い `ece-tools` 2002.1.x、新しい [フック設定](https://experienceleague.adobe.com/docs/commerce-cloud-service/user-guide/configure/app/properties/hooks-property.html) 形式を使用します。 以前の形式 `ece-tools` 2002.0.x リリースは引き続きサポートされます。 ただし、シナリオベースのデプロイメント機能を使用するには、を新しい形式に更新する必要があります。

## シナリオ手順の確認

フック設定では、各シナリオは、ビルド、デプロイ、デプロイ後のタスクを実行する手順を含む XML ファイルです。 例えば、 `scenario/transfer` ファイルには、次の 3 つの手順が含まれます。 `compress-static-content`, `clear-init-directory`、および `backup-data`

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

## デフォルトのシナリオの拡張

次の例では、デフォルトのデプロイシナリオを拡張して、追加のデプロイ設定ファイルをフック設定に追加しています。

```yaml
hooks:
  deploy: |
    php ./vendor/bin/ece-tools run scenario/deploy.xml vendor/<vendor-name>/<module-name>/deploy.xml vendor/<vendor-name>/<module-name>/deploy2.xml
```

デプロイメント中に、カスタムシナリオが次のルールに基づいてデフォルトのシナリオと結合されます。

- シナリオは、フック定義内の順序に基づいて優先順位付けされ、リストに表示された最後のシナリオの優先順位が最も高くなります。

  この例では、シナリオは次の優先順位を持ちます。

   1. `vendor/vendor-name/module-name/deploy2.xml`
   1. `vendor/vendor-name/module-name/deploy.xml`
   1. `scenario/deploy.xml` （デフォルトまたはベースラインのシナリオ）

- 優先順位が最も高いシナリオのステップは、他のシナリオで同じ名前のステップを上書きします。 設定に新しい手順が追加されました。 同じルールが 2 つ以上のシナリオに適用され、各シナリオが右から左に優先付けされます ( 例：C → B → A)。

### デフォルトの手順を削除

デフォルトのシナリオから手順を削除するには、 `skip` パラメーター。

例えば、 `enable-maintenance-mode` および `set-production-mode` デフォルトのデプロイシナリオの手順では、次の設定を含む設定ファイルを作成します。

> `vendor/vendor-name/module-name/deploy-custom-mode-config.xml`

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
    <step name="enable-maintenance-mode" skip="true" />
    <step name="set-production-mode"  skip="true" />
</scenario>
```

カスタム設定ファイルを使用するには、デフォルトの `.magento.app.yaml` ファイル。

> `.magento.app.yaml` カスタムデプロイシナリオとの併用

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

### デフォルトのステップの置き換え

カスタムシナリオは、デフォルトの手順を置き換えて、カスタム実装を提供できます。 これをおこなうには、デフォルトのステップ名をカスタムステップの名前として使用します。

例えば、 [デフォルトのデプロイシナリオ] の `enable-maintenance-mode` ステップはデフォルトを実行します [EnableMaintenanceMode PHP スクリプト].

```xml
<step name="enable-maintenance-mode" type="Magento\MagentoCloud\Step\EnableMaintenanceMode" priority="300"/>
```

この手順を上書きするには、カスタムシナリオ設定ファイルを作成して、 `enable-maintenance-mode` ステップが実行されます。

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
<scenario>
    <step name="enable-maintenance-mode" type="VendorName\VendorModule\Step\CustomEnableMaintenanceMode" priority="300"/>
</scenario>
```

### ステップの優先度の変更

カスタムシナリオでは、デフォルトの手順の優先度を変更できます。 次の手順で、 `enable-maintenance-mode` ～から足を踏み出す `300` から `10` デプロイシナリオの前の手順を実行するために使用します。

```xml
<?xml version="1.0"?>
<scenario xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:ece-tools:config/scenario.xsd">
<scenario>
    <step name="enable-maintenance-mode" type="Magento\MagentoCloud\Step\EnableMaintenanceMode" priority="10"/>
</scenario>
```

この例では、 `enable-maintenance-mode` この手順は、デフォルトのデプロイシナリオの他のすべての手順よりも優先度が低いので、シナリオの先頭に移動します。

### 例：デプロイシナリオの拡張

次の例では、 [デフォルトのデプロイシナリオ] を次のように変更します。

- を `remove-deploy-failed-flag` カスタムステップを使用する
- をスキップする `clean-redis-cache` デプロイ前の手順のサブ手順
- をスキップする `unlock-cron-jobs` step
- をスキップする `validate-config` 重要なバリデーターを無効にする手順
- 新しいデプロイ前の手順を追加します

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

このスクリプトをプロジェクトで使用するには、次の設定を `.magento.app.yaml` Adobe Commerce on cloud infrastructure プロジェクト用のファイル：

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
>以下を実行すると、 [デフォルトのシナリオ](https://github.com/magento/ece-tools/tree/2002.1/scenario) および [デフォルトのステップ設定](https://github.com/magento/ece-tools/tree/2002.1/src/Step) （内） `ece-tools` GitHub リポジトリを使用して、プロジェクトのビルド、デプロイ、デプロイ後のタスクに合わせてカスタマイズするシナリオと手順を決定します。

## 拡張するカスタムモジュールの追加 `ece-tools`

The `ece-tools` パッケージには、セマンティックバージョン標準に従ったデフォルトの API インターフェイスが用意されています。 すべての API インターフェイスは、 **@api** 注釈。 カスタムモジュールを作成し、必要に応じてデフォルトのコードを変更することで、デフォルトの API 実装を独自の実装に置き換えることができます。

クラウドインフラストラクチャ上のAdobe Commerceでカスタムモジュールを使用するには、モジュールを `ece-tools` パッケージ。 登録プロセスは、Adobe Commerceでのモジュールの登録に使用するプロセスと似ています。

**モジュールを `ece-tools` パッケージ**:

1. を作成または拡張する `registration.php` ファイルのパスを変更します。

   ```php?start_inline=1
   \Magento\MagentoCloud\ExtensionRegistrar::register('module-name', __DIR__);
   ```

1. を更新します。 `autoload` モジュール設定ファイルに含めるセクション `registration.php` モジュールファイルを自動ロードするファイル `composer.json`.

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

1. 次を追加： `config/services.xml` ファイルをモジュールに挿入します。 この設定は `config/services.xml` から `ece-tools` パッケージ。

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

依存関係のインジェクションの詳細については、 [Symfony 依存関係の挿入](https://symfony.com/doc/current/components/dependency_injection.html).

<!-- link definitions -->

[デフォルトのデプロイシナリオ]: https://github.com/magento/ece-tools/blob/develop/scenario/deploy.xml
[EnableMaintenanceMode PHP スクリプト]: https://github.com/magento/ece-tools/blob/develop/src/Step/EnableMaintenanceMode.php
