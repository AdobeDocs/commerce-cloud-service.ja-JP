---
title: デプロイメントの追跡
description: New Relicを設定して、クラウドインフラストラクチャプロジェクト上のAdobe Commerceのデプロイメントをトラッキングし、パフォーマンスの変化を分析する方法について説明します。
feature: Cloud, Deploy, Observability
topic: Performance
last-substantial-update: 2023-10-12T00:00:00Z
exl-id: 3d477a4b-ae5a-4c82-b71b-33ff24827b93
source-git-commit: cd9b541bd5b2fa342b76c992ab85a5115368313b
workflow-type: tm+mt
source-wordcount: '283'
ht-degree: 0%

---

# デプロイメントの追跡

New Relicを有効にできます _変更の追跡_ クラウドインフラストラクチャプロジェクト上のCommerceのデプロイメントイベントを監視する機能。

Deployments データ収集は、CPU、メモリ、応答時間など、全体的なパフォーマンスに対するデプロイメントの変更の影響を分析するのに役立ちます。 参照： [NerdGraph を使用した変更の追跡](https://docs.newrelic.com/docs/change-tracking/change-tracking-graphql/) が含まれる _New Relic ドキュメント_.

>[!PREREQUISITES]
>
>- `NR_API_URL`:New Relic API エンドポイント（ここでは NerdGraph API URL） `https://api.newrelic.com/graphql`
>- `NR_API_KEY`：ユーザーキーを作成します。を参照してください [New Relicの API キー](https://docs.newrelic.com/docs/apis/intro-apis/new-relic-api-keys) が含まれる _New Relic_ ドキュメント。
>- `NR_APP_GUID`:New Relicにデータをレポートするエンティティには、一意の ID （GUID）があります。 例えば、ステージング環境でを有効にするには、ステージング環境を調整します `NR_APP_GUID` を使用したクラウド変数 _ステージングエンティティ GUID_ New Relicから を参照してください。 [New Relic エンティティについて学ぶ](https://docs.newrelic.com/docs/new-relic-solutions/new-relic-one/core-concepts/what-entity-new-relic/) および [NerdGraph チュートリアル：エンティティ データの表示](https://docs.newrelic.com/docs/apis/nerdgraph/examples/nerdgraph-entities-api-tutorial/) が含まれる _New Relic_ ドキュメント。

## デプロイメントの追跡を有効にする

を作成して、New RelicでCommerce プロジェクトのデプロイメントイベントを追跡します。 _script_ 統合。

**トラック デプロイメントを有効にするには**:

1. ローカルワークステーションで、をプロジェクトディレクトリに変更します。
1. を作成 `action-integration.js` ファイル。 以下のコードをコピーして、に貼り付けます。 `action-integration.js` ファイルと保存：

   ```javascript
   function trackDeployments() {
     const envName = activity.payload.environment.name;
     let variables;
     activity.payload.deployment.variables.forEach(function(variable) {
       if (variable.name === "env:NR_CONFIG") {
         variables = variable.value;
       }
     });
     const config = JSON.parse(variables.replace(/'/g, '"'));
     const commitSha = activity.payload.commits ? activity.payload.commits[0].sha : activity.payload.environment.head_commit;
     const deploymentType = activity.type;
   
     if (!(envName in config)) {
       throw new Error('There is no configuration for ' + envName);
     }
   
     const configEnv = config[envName];
   
     if (!configEnv.NR_APP_GUID || !configEnv.NR_API_KEY || !configEnv.NR_API_URL) {
       throw new Error('You must define the next configuation in the env variable NR_CONFIG: NR_APP_GUID, NR_API_KEY and NR_API_URL');
     }
   
     const query = `mutation {
       changeTrackingCreateDeployment(
       deployment: {
           version: "${commitSha}",
           entityGuid: "${configEnv.NR_APP_GUID}",
           commit: "${commitSha}",
           changelog: "${deploymentType}"
       }
       ) {
         deploymentId
         entityGuid
       }
     }`;
   
     var resp = fetch(configEnv.NR_API_URL, {
       method: 'POST',
       headers: {
           'Content-Type': 'application/json',
           'API-Key': configEnv.NR_API_KEY
       },
       body: JSON.stringify({
           query
       })
     });
   
     if (!resp.ok) {
       console.log('Sending new relic change tracking failed: ' + resp.text());
     } else {
       console.log(resp.text());
     }
   }
   
   trackDeployments();
   ```

1. を作成 _script_ を使用したの統合 `magento-cloud` CLI コマンドと参照 `action-integration.js` ファイル。

   ```bash
   magento-cloud integration:add --type script --events='environment.restore, environment.push, environment.branch, environment.activate, environment.synchronize, environment.initialize, environment.merge, environment.redeploy, environment.variable.create, environment.variable.delete, environment.variable.update' --file ./action-integration.js --project=<YOUR_PROJECT_ID> --environments=<YOUR_ENVIRONMENT_ID>
   ```

   応答の例：

   ```terminal
   Created integration 767u4hathojjw (type: script)
   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
   | Property              | Value                                                                                                                                   |
   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
   | id                    | 767u4hathojjw                                                                                                                           |
   | type                  | script                                                                                                                                  |
   | role                  |                                                                                                                                         |
   | events                | - environment.restore                                                                                                                   |
   |                       | - environment.push                                                                                                                      |
   |                       | - environment.branch                                                                                                                    |
   |                       | - environment.activate                                                                                                                  |
   |                       | - environment.synchronize                                                                                                               |
   |                       | - environment.initialize                                                                                                                |
   |                       | - environment.merge                                                                                                                     |
   |                       | - environment.redeploy                                                                                                                  |
   |                       | - environment.variable.create                                                                                                           |
   |                       | - environment.variable.delete                                                                                                           |
   |                       | - environment.variable.update                                                                                                           |
   | environments          | - staging                                                                                                                               |
   |                       | - production                                                                                                                            |
   | excluded_environments | {  }                                                                                                                                    |
   | states                | - complete                                                                                                                              |
   | result                | *                                                                                                                                       |
   | script                | function variables() {                                                                                                                  |
   |                       |     var vars = {};                                                                                                                      |
   |                       |     activity.payload.deployment.variables.forEach(function(variable) {                                                                  |
   |                       |         vars[variable.name] = variable.value;                                                                                           |
   |                       |     });                                                                                                                                 |
   |                       |     return vars;                                                                                                                        |
   |                       | }                                                                                                                                       |
   |                       |                                                                                                                                         |
   |                       | function trackDeployments() {                                                                                                           |
   |                       |     const envName = activity.payload.environment.name;                                                                                  |
   |                       |                                                                                                                                         |
   |                       |     const config = JSON.parse(variables()['env:NR_CONFIG'].replace(/'/g, '"'));                                                         |
   |                       |     const commitSha = activity.payload.commits ? activity.payload.commits[0].sha : activity.payload.environment.head_commit;            |
   |                       |     const deploymentType = activity.type;                                                                                               |
   |                       |                                                                                                                                         |
   |                       |     if (!(envName in config)) {                                                                                                         |
   |                       |         throw new Error('There is no configuration for ' + envName);                                                                    |
   |                       |     }                                                                                                                                   |
   |                       |                                                                                                                                         |
   |                       |     const configEnv = config[envName];                                                                                                  |
   |                       |                                                                                                                                         |
   |                       |     if (!configEnv.NR_APP_GUID || !configEnv.NR_API_KEY || !configEnv.NR_API_URL) {                                                     |
   |                       |         throw new Error('You must define the next configuation in the env variable NR_CONFIG: NR_APP_GUID, NR_API_KEY and NR_API_URL'); |
   |                       |     }                                                                                                                                   |
   |                       |                                                                                                                                         |
   |                       |     const query = `mutation {                                                                                                           |
   |                       |         changeTrackingCreateDeployment(                                                                                                 |
   |                       |           deployment: {                                                                                                                 |
   |                       |             version: "${commitSha}",                                                                                                    |
   |                       |             entityGuid: "${configEnv.NR_APP_GUID}",                                                                                     |
   |                       |             commit: "${commitSha}",                                                                                                     |
   |                       |             changelog: "${deploymentType}"                                                                                              |
   |                       |           }                                                                                                                             |
   |                       |         ) {                                                                                                                             |
   |                       |           deploymentId                                                                                                                  |
   |                       |           entityGuid                                                                                                                    |
   |                       |         }                                                                                                                               |
   |                       |     }`;                                                                                                                                 |
   |                       |                                                                                                                                         |
   |                       |     var resp = fetch(configEnv.NR_API_URL, {                                                                                            |
   |                       |         method: 'POST',                                                                                                                 |
   |                       |         headers: {                                                                                                                      |
   |                       |             'Content-Type': 'application/json',                                                                                         |
   |                       |             'API-Key': configEnv.NR_API_KEY                                                                                             |
   |                       |         },                                                                                                                              |
   |                       |         body: JSON.stringify({                                                                                                          |
   |                       |             query                                                                                                                       |
   |                       |         })                                                                                                                              |
   |                       |     });                                                                                                                                 |
   |                       |                                                                                                                                         |
   |                       |     if (!resp.ok) {                                                                                                                     |
   |                       |         console.log('Sending new relic change tracking failed: ' + resp.text());                                                        |
   |                       |     } else {                                                                                                                            |
   |                       |         console.log(resp.text());                                                                                                       |
   |                       |     }                                                                                                                                   |
   |                       | }                                                                                                                                       |
   |                       |                                                                                                                                         |
   |                       | trackDeployments();                                                                                                                     |
   |                       |                                                                                                                                         |
   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------+
   ```

1. 後で使用するために、統合 ID をメモします。 この例では、ID はです。

   ```terminal
   Created integration 767u4hathojjw (type: script)
   ```

   オプションで、統合を検証し、次を使用して統合 ID をメモすることができます。 `magento-cloud integration:list`

1. 前提条件を使用して環境変数を作成します。

   ```bash
   magento-cloud variable:create --level project --name=env:NR_CONFIG --value='{"<YOUR_ENVIRONMENT_ID>":{"NR_API_KEY": "<YOUR_API_KEY>", "NR_API_URL": "https://api.newrelic.com/graphql", "NR_APP_GUID":"<YOUR_APP_GUID>"}}'  -p <YOUR_PROJECT_ID>
   ```

1. 最後のアクティビティログを確認します。

   ```bash
   magento-cloud integration:activity:log <INTEGRATION_ID> -p <YOUR_PROJECT_ID> -e <YOUR_ENVIRONMENT_ID>
   ```

   応答：

   ```terminal
   Integration ID: 767u4hathojjw
   Activity ID: poxqidsfajkmg
   Type: integration.script
   Description: Running activity script
   Created: 2023-08-28T20:32:02+00:00
   State: complete
   Log:
   HTTP request
   HTTP response
   {"data":{"changeTrackingCreateDeployment":{"deploymentId":"some-deployment-id","entityGuid":"SomeGUIDhere"}}}
   ```

1. にログイン [New Relic アカウント](https://login.newrelic.com/login).

1. エクスプローラーナビゲーションメニューで、 **[!UICONTROL APM & Services]**. 環境を選択 [!UICONTROL Name] および [!UICONTROL Account].

1. 次の下 _イベント_&#x200B;を選択し、 **[!UICONTROL Change tracking]**.

   ![デプロイメント](../../assets/new-relic/deployments.png)
