---
title: 変数の作成
description: クラウドインフラストラクチャ構築フェーズでのAdobe Commerceのアクションを制御する環境変数の一覧を参照してください。
feature: Cloud, Configuration, Build, SCD, Upgrade
recommendations: noDisplay, catalog
role: Developer
exl-id: 243aaa45-a5ef-4ed2-8800-3d0f07bf3740
source-git-commit: 7b9c6a4cd17069c25455195bd8f273664b8a29eb
workflow-type: tm+mt
source-wordcount: '920'
ht-degree: 0%

---

# 変数の作成

次の _ビルド_ 変数は、ビルドフェーズのアクションを制御し、 [グローバル変数](variables-global.md). 次の変数を `build` 段階 `.magento.env.yaml` ファイル：

```yaml
stage:
  build:
    BUILD_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズについて詳しくは、次の手順を参照してください。

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

v2.2 では次の変数が削除されました。

- `skip_di_clearing`
- `skip_di_compilation`

## `ERROR_REPORT_DIR_NESTING_LEVEL`

- **デフォルト**—`1`
- **バージョン**—Adobe Commerce 2.1.4 以降

エラーレポートファイルを保存するディレクトリのネストのレベルを設定して、何万ものファイルがレポートディレクトリに格納されるのを防ぎ、データの管理や確認を困難にする可能性があります。 この設定のデフォルト値はです。 `1`. 通常は、デフォルト値を変更する必要はありません。ただし、 `<magento_root>/var/report/` ディレクトリ。

```yaml
stage:
  build:
    ERROR_REPORT_DIR_NESTING_LEVEL: 2
```

## `QUALITY_PATCHES`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

デプロイ時に適用するAdobe Commerce Quality パッチのリストを指定します。

```yaml
stage:
  build:
    QUALITY_PATCHES: [ ]
```

次の例では、デプロイ時に適用する 3 つのパッチを指定します。

```yaml
stage:
  build:
    QUALITY_PATCHES:
      - MC-31387
      - MDVA-4567
      - MC-456345
```

詳しくは、 [パッチの適用](../development/apply-patches.md).

## `SCD_COMPRESSION_LEVEL`

- **デフォルト**—`6`
- **バージョン**—Adobe Commerce 2.1.4 以降

どれを指定するかを指定します [gzip](https://www.gnu.org/software/gzip) 圧縮レベル (`0` から `9`) を使用して、静的コンテンツを圧縮する場合に使用します。 `0` 圧縮を無効にします。

```yaml
stage:
  build:
    SCD_COMPRESSION_LEVEL: 4
```

## `SCD_COMPRESSION_TIMEOUT`

- **デフォルト**—`600`
- **バージョン**—Adobe Commerce 2.1.4 以降

静的アセットの圧縮に要する時間が圧縮タイムアウトの制限を超えると、デプロイメントプロセスが中断されます。 静的コンテンツ圧縮コマンドの最大実行時間を秒単位で設定します。

```yaml
stage:
  build:
    SCD_COMPRESSION_TIMEOUT: 800
```

## `SCD_NO_PARENT`

- **デフォルト**—`false`
- **バージョン**—Adobe Commerce 2.4.2 以降

をに設定します。 `true` ビルドフェーズ中に親テーマ用の静的コンテンツが生成されるのを防ぐ。

設定 `SCD_NO_PARENT: false` 親テーマの静的コンテンツを生成しても、サイトの展開に影響を与えず、不要なサイトのダウンタイムを引き起こさないように、ビルドフェーズ中に行われます。 詳しくは、 [静的コンテンツのデプロイメント](../deploy/static-content.md).

```yaml
stage:
  build:
    SCD_NO_PARENT: false
```

## `SCD_MATRIX`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

テーマごとに複数のロケールを設定できます。 このカスタマイズにより、不要なテーマファイルの数を減らし、ビルドプロセスを迅速に実行できます。 例えば、 _magento/backend_ 英語のテーマと他の言語のカスタムテーマ。

次の例では、 `Magento/backend` 3 つのロケールを持つテーマ

```yaml
stage:
  build:
    SCD_MATRIX:
      "Magento/backend":
        language:
          - en_US
          - fr_FR
          - af_ZA
```

次の例では、3 つのロケールを持つ 3 つのテーマを作成します。

```yaml
stage:
  build:
    SCD_MATRIX:
      "Magento/backend":
        language:
          - en_US
          - fr_FR
          - af_ZA
      "Magento/blank":
        language:
          - en_US
          - fr_FR
          - af_ZA
      "Magento/luma":
        language:
          - en_US
          - fr_FR
          - af_ZA
```

または、次のいずれかを選択できます。 _not_ テーマのデプロイ：

```yaml
stage:
  build:
    SCD_MATRIX:
      "Magento/backend": [ ]
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**— Adobe Commerce 2.2.0 以降

静的コンテンツデプロイメントの予想される最大実行時間を増やすことができます。

デフォルトでは、Adobe Commerce on cloud infrastructure では、期待される最大実行時間が 900 秒に設定されますが、場合によっては、クラウドプロジェクトの静的コンテンツのデプロイメントを完了するのに、より長い時間が必要になることがあります。

```yaml
stage:
  build:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_STRATEGY`

- **デフォルト**—`quick`
- **バージョン**— Adobe Commerce 2.2.0 以降

のカスタマイズ [デプロイメント戦略](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-strategy.html) 静的コンテンツ用。 詳しくは、 [静的ビューファイルのデプロイ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html).

次のオプションを使用 _のみ_ 複数のロケールがある場合：

- `standard` — すべてのパッケージのすべての静的ビューファイルをデプロイします。
- `quick`—(_デフォルト_) により、デプロイメント時間を最小限に抑えることができます。
- `compact` — サーバ上のディスク領域を節約します。 Adobe Commerceバージョン 2.2.4 以前では、この設定がの値よりも優先されます。 `scd_threads` 値を持つ `1`.

```yaml
stage:
  build:
    SCD_STRATEGY: "compact"
```

## `SCD_THREADS`

- **デフォルト** — 自動
- **バージョン**—Adobe Commerce 2.1.4 以降

静的コンテンツデプロイメントのスレッド数を設定します。 デフォルト値は、検出された CPU スレッド数に基づいて設定され、4 を超えない値です。 スレッド数を増やすと、静的コンテンツのデプロイメントが高速化します。スレッド数を減らすと、デプロイメントの速度が低下します。 スレッド値は次のように設定できます。

```yaml
stage:
  build:
    SCD_THREADS: 2
```

デプロイメント時間をさらに短縮するには、 [設定管理](../store/store-settings.md) と `scd-dump` コマンドを使用して、静的デプロイメントをビルドフェーズに移動します。

## `SCD_USE_BALER`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.3.0 以降

[Baler](https://github.com/magento/baler) は、生成された JavaScript コードをスキャンし、最適化された JavaScript バンドルを作成します。 最適化されたバンドルをサイトにデプロイすると、サイトの読み込み時のネットワークリクエストの数を減らし、ページの読み込み時間を短縮できます。

をに設定します。 `true` 静的コンテンツのデプロイメントを実行した後に Baler を実行する。

```yaml
stage:
  build:
    SCD_USE_BALER: true
```

>[!NOTE]
>
>Baler はアルファリリースなので、実稼動環境では使用しないことをお勧めします。

## `SKIP_COMPOSER_DUMP_AUTOLOAD`

- **デフォルト**— _未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

をに設定します。 `true` 飛び出す `composer dump-autoload` コマンドを使用して、Cloud Docker のインストール中に実行する必要があります。 この変数は、書き込み可能なファイルシステムを持つ Cloud Docker コンテナにのみ関連します。 この場合、コマンドをスキップすると、削除されたコードからコードにアクセスしようとする他のコマンドでエラーが発生するのを防ぎます `generated` ディレクトリ。

Adobe Commerceの実行時 `composer dump-autoload`を使用すると、 `generated` フォルダ（読み取り専用ファイルシステムを使用する実稼動環境では問題になりません）。 ただし、書き込み可能なファイルシステムを使用した Cloud Docker インストールの場合（を使用したテストおよび開発のみで作成）、 `./vendor/bin/ece-docker build:compose --with-test`) を使用する場合、 `bin/magento -n setup:upgrade` コマンドを `--keep-generated` オプション（削除） `generated` ディレクトリ。 ディレクトリが削除された場合、 `composer dump-autoload` autoload に削除されたディレクトリ内のファイルへのリンクが含まれているため、コマンドは失敗します。

```yaml
stage:
  build:
    SKIP_COMPOSER_DUMP_AUTOLOAD: true
```

## `SKIP_SCD`

- **デフォルト**— _未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

をに設定します。 `true` を使用して、ビルドフェーズ中に静的コンテンツのデプロイメントをスキップできます。

を使用して、ビルドフェーズで既に静的コンテンツをデプロイしている場合。 [設定管理](../store/store-settings.md)を使用する場合は、静的コンテンツのデプロイメントをスキップして、クイックビルドテストをおこなうことができます。

ビルドフェーズで、 `SKIP_SCD: false` したがって、静的コンテンツのビルドは、プロセスがサイトのデプロイメントに影響を与えず、不要なサイトのダウンタイムを引き起こさないビルドフェーズ中に行われます。 詳しくは、 [静的コンテンツのデプロイメント](../deploy/static-content.md).

```yaml
stage:
  build:
    SKIP_SCD: false
```

## `VERBOSE_COMMANDS`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

を有効または無効にする [Symfony](https://symfony.com/doc/current/console/verbosity.html) の詳細レベルをデバッグ `bin/magento` 導入フェーズ中に実行される CLI コマンド。

>[!NOTE]
>
>VERBOSE_COMMANDS を使用して、成功したコマンドと失敗したコマンドの出力の詳細を制御するには `bin/magento` CLI コマンドを使用する場合は、 [MIN_LOGGING_LEVEL](variables-global.md#minlogginglevel) `debug`.

ログに表示する詳細レベルを選択します。

- `-v`=通常の出力
- `-vv`=より詳細な出力
- `-vvv` =デバッグに最適な詳細出力

```yaml
stage:
  build:
    VERBOSE_COMMANDS: "-vv"
```
