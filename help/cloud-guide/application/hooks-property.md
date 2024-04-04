---
title: Hooks プロパティ
description: でフックプロパティを設定する方法の例を参照してください。 [!DNL Commerce] アプリケーション設定ファイル。
feature: Cloud, Configuration, Build, Deploy
exl-id: d9561f09-5129-4b72-978e-2e3873e8efae
source-git-commit: eace5d84fa0915489bf562ccf79fde04f6b9d083
workflow-type: tm+mt
source-wordcount: '316'
ht-degree: 0%

---

# Hooks プロパティ

以下を使用します。 `hooks` ビルド、デプロイ、およびデプロイ後のフェーズでシェルコマンドを実行するセクション：

- **`build`** — コマンドを実行します。 _前_ アプリケーションをパッケージ化します。 アプリケーションがまだデプロイされていないので、データベースや Redis などのサービスは使用できません。 カスタムコマンドを追加 _前_ デフォルト `php ./vendor/bin/ece-tools` コマンドを使用して、カスタム生成されたコンテンツをデプロイメントフェーズまで引き続きおこなうことができます。

- **`deploy`** — コマンドを実行します。 _次より後_ アプリケーションのパッケージ化とデプロイを行います。 この時点で他のサービスにアクセスできます。 デフォルト以降 `php ./vendor/bin/ece-tools` コマンドは、 `app/etc` ディレクトリを正しい場所に配置するには、カスタムコマンドを追加する必要があります _次より後_ deploy コマンドを使用して、カスタムコマンドが失敗するのを防ぎます。

- **`post_deploy`** — コマンドを実行します。 _次より後_ アプリケーションのデプロイと _次より後_ コンテナが接続の受け入れを開始します。 The `post_deploy` フックはキャッシュをクリアし、キャッシュをプリロード (warms) します。 ページのリストは、 `WARM_UP_PAGES` 変数を [デプロイ後のステージ](../environment/variables-post-deploy.md). 必須ではありませんが、これは `SCD_ON_DEMAND` 環境変数。

次の例は、 `.magento.app.yaml` ファイル。 CLI コマンドを `build`, `deploy`または `post_deploy` セクション _前_ の `ece-tools` コマンド：

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

また、 `generate` および `transfer` コードを特別に作成またはファイルを移動する際に追加のアクションを実行するコマンド。

```yaml
hooks:
    # We run build hooks before your application has been packaged.
    build: |
        set -e
        php ./vendor/bin/ece-tools build:generate
        # php /path/to/your/script
        php ./vendor/bin/ece-tools build:transfer
```

- `set -e` — 最後に失敗したコマンドではなく、最初に失敗したコマンドでフックを失敗させます。
- `build:generate`- SCD がビルドフェーズに対して有効な場合は、パッチを適用し、設定を検証し、DI を生成し、静的コンテンツを生成します。
- `build:transfer` — 生成されたコードと静的コンテンツを最終的な宛先に転送します。

コマンドは、アプリケーションから実行されます (`/app`) ディレクトリに保存されます。 以下を使用すると、 `cd` コマンドを使用してディレクトリを変更します。 フックの最後のコマンドが失敗すると、フックは失敗します。 最初に失敗したコマンドで失敗させるには、 `set -e` フックの先頭に

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

次を使用して Sass ファイルをコンパイル `grunt` 静的コンテンツをデプロイする前（ビルド中に発生）。 を `grunt` コマンドを `build` コマンドを使用します。

{{scenarios}}
