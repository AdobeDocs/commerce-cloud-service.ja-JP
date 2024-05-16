---
title: Hooks プロパティ
description: で hooks プロパティを設定する方法の例を参照してください [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration, Build, Deploy
exl-id: d9561f09-5129-4b72-978e-2e3873e8efae
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '316'
ht-degree: 0%

---

# Hooks プロパティ

の使用 `hooks` 構築、配備、および配備フェーズ後にシェルコマンドを実行するセクション：

- **`build`** – コマンドを実行する _次の前_ アプリケーションのパッケージ化。 アプリケーションがまだデプロイされていないので、データベースや Redis などのサービスは使用できません。 カスタムコマンドの追加 _次の前_ デフォルト `php ./vendor/bin/ece-tools` カスタム生成コンテンツがデプロイメントフェーズを続行するようにコマンドを実行します。

- **`deploy`** – コマンドを実行する _後_ アプリケーションのパッケージ化とデプロイ。 この時点で他のサービスにアクセスできます。 デフォルト以降 `php ./vendor/bin/ece-tools` コマンドは、 `app/etc` 正しい場所へのディレクトリです。カスタム コマンドを追加する必要があります _後_ カスタムコマンドが失敗するのを防ぐための deploy コマンド。

- **`post_deploy`** – コマンドを実行する _後_ アプリケーションのデプロイと _後_ コンテナが接続の受け入れを開始します。 この `post_deploy` フックはキャッシュをクリアし、キャッシュをプリロード（warms）します。 ページのリストをカスタマイズするには、 `WARM_UP_PAGES` 内の変数 [デプロイ後のステージ](../environment/variables-post-deploy.md). 必須ではありませんが、と並行して動作します `SCD_ON_DEMAND` 環境変数。

次の例は、のデフォルト設定を示しています `.magento.app.yaml` ファイル。 の下に CLI コマンドを追加する `build`, `deploy`、または `post_deploy` セクション _次の前_ この `ece-tools` コマンド：

```yaml
hooks:
    # We run build hooks before your application has been packaged.
    build: |
        set -e
        composer install
        php ./vendor/bin/ece-tools run scenario/build/generate.xml
        php ./vendor/bin/ece-tools run scenario/build/transfer.xml
    # We run deploy hook after your application has been deployed and started.
    deploy: |
        php ./vendor/bin/ece-tools run scenario/deploy.xml
    # We run post deploy hook to clean and warm the cache. Available with ECE-Tools 2002.0.10.
    post_deploy: |
        php ./vendor/bin/ece-tools run scenario/post-deploy.xml
```

また、を使用して、ビルドフェーズをさらにカスタマイズできます。 `generate` および `transfer` コードを特別に作成したりファイルを移動したりする際に、追加のアクションを実行するコマンド。

```yaml
hooks:
    # We run build hooks before your application has been packaged.
    build: |
        set -e
        php ./vendor/bin/ece-tools build:generate
        # php /path/to/your/script
        php ./vendor/bin/ece-tools build:transfer
```

- `set -e` – 最後の失敗したコマンドではなく、最初に失敗したコマンドでフックを失敗させます。
- `build:generate`- SCD が構築フェーズで有効な場合、パッチの適用、構成の検証、DI の生成、静的コンテンツの生成を行います。
- `build:transfer` – 生成されたコードと静的コンテンツを最終的な宛先に転送します。

コマンドはアプリケーションから実行します（`/app`） ディレクトリ。 を使用できます `cd` ディレクトリを変更するコマンド。 フックは、最終的なコマンドが失敗すると失敗します。 最初の失敗したコマンドで失敗させるには、次を追加します `set -e` フックの先頭に。

**grunt を使用して Sass ファイルをコンパイルするには**:

```yaml
dependencies:
    ruby:
        sass: "3.4.7"
    nodejs:
        grunt-cli: "~0.1.13"

hooks:
    build: |
        cd public/profiles/project_name/themes/custom/theme_name
        npm install
        grunt
        cd
        php ./vendor/bin/ece-tools build
```

を使用した Sass ファイルのコンパイル `grunt` ビルド中に行われる静的コンテンツのデプロイメントの前。 を配置 `grunt` の前のコマンド `build` コマンド。

{{scenarios}}
