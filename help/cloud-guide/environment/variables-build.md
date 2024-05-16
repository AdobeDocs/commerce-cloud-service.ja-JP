---
title: ビルド変数
description: クラウドインフラストラクチャー上のAdobe Commerceのビルドフェーズでアクションを制御する環境変数のリストを参照してください。
feature: Cloud, Configuration, Build, SCD, Upgrade
recommendations: noDisplay, catalog
role: Developer
exl-id: 243aaa45-a5ef-4ed2-8800-3d0f07bf3740
source-git-commit: 7b9c6a4cd17069c25455195bd8f273664b8a29eb
workflow-type: tm+mt
source-wordcount: '920'
ht-degree: 0%

---

# ビルド変数

次の _ビルド_ 変数は、ビルドフェーズでのアクションを制御し、から値を継承および上書きできます。 [グローバル変数](variables-global.md). これらの変数を `build` ステージ `.magento.env.yaml` ファイル：

```yaml
stage:
  build:
    BUILD_VARIABLE_NAME: value
```

ビルドおよびデプロイプロセスのカスタマイズに関する詳細情報：

- [デプロイメント設定](configure-env-yaml.md)
- [デプロイメントプロセス](../deploy/process.md)

v2.2 では、次の変数が削除されました。

- `skip_di_clearing`
- `skip_di_compilation`

## `ERROR_REPORT_DIR_NESTING_LEVEL`

- **デフォルト**—`1`
- **バージョン**—Adobe Commerce 2.1.4 以降

エラーレポートファイルの保存に使用するディレクトリネストのレベルを設定して、レポートディレクトリが数万のファイルで埋もれてしまい、データの管理やレビューが困難になるのを回避します。 この設定のデフォルトは `1`. 通常、でエラーレポートファイルを管理する際に問題が発生していない限り、デフォルト値を変更する必要はありません `<magento_root>/var/report/` ディレクトリ。

```yaml
stage:
  build:
    ERROR_REPORT_DIR_NESTING_LEVEL: 2
```

## `QUALITY_PATCHES`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

デプロイ時に適用するAdobe Commerce品質パッチのリストを指定します。

```yaml
stage:
  build:
    QUALITY_PATCHES: [ ]
```

次の例では、デプロイメント時に適用する 3 つのパッチを指定します。

```yaml
stage:
  build:
    QUALITY_PATCHES:
      - MC-31387
      - MDVA-4567
      - MC-456345
```

参照： [パッチの適用](../development/apply-patches.md).

## `SCD_COMPRESSION_LEVEL`

- **デフォルト**—`6`
- **バージョン**—Adobe Commerce 2.1.4 以降

を指定します [gzip](https://www.gnu.org/software/gzip) 圧縮レベル （`0` 対象： `9`）を選択して、静的コンテンツの圧縮時に使用します。 `0` 圧縮を無効にします。

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

をに設定 `true` ビルドフェーズで親テーマの静的コンテンツが生成されるのを防ぎます。

を設定 `SCD_NO_PARENT: false` 親テーマの静的コンテンツを生成してもサイトのデプロイメントに影響を与えたり、不要なサイトのダウンタイムを引き起こしたりしないように、ビルドフェーズ中に設定します。 参照： [静的コンテンツデプロイメント](../deploy/static-content.md).

```yaml
stage:
  build:
    SCD_NO_PARENT: false
```

## `SCD_MATRIX`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

テーマごとに複数のロケールを設定できます。 このカスタマイズにより、不要なテーマファイルの数を減らすことで、ビルドプロセスを迅速化できます。 例えば、 _magento/バックエンド_ 英語のテーマおよび他の言語のカスタムテーマ。

次の例では、をビルドします `Magento/backend` 3 つのロケールを持つテーマ：

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

次の例では、3 つのロケールで 3 つのテーマを構築します。

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

または、次のいずれかを選択できます。 _ではない_ テーマをデプロイします。

```yaml
stage:
  build:
    SCD_MATRIX:
      "Magento/backend": [ ]
```

## `SCD_MAX_EXECUTION_TIME`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.2.0 以降

静的コンテンツのデプロイメントの予想最大実行時間を増やすことができます。

デフォルトでは、クラウドインフラストラクチャー上のAdobe Commerceでは、予想される最大実行時間が 900 秒に設定されていますが、場合によっては、クラウドプロジェクトの静的コンテンツのデプロイメントを完了するためにより多くの時間が必要になることがあります。

```yaml
stage:
  build:
    SCD_MAX_EXECUTION_TIME: 3600
```

{{scd-timing-warning}}

## `SCD_STRATEGY`

- **デフォルト**—`quick`
- **バージョン**—Adobe Commerce 2.2.0 以降

のカスタマイズ [デプロイメント戦略](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-strategy.html) 静的コンテンツの場合。 参照： [静的表示ファイルのデプロイ](https://experienceleague.adobe.com/docs/commerce-operations/configuration-guide/cli/static-view/static-view-file-deployment.html).

次のオプションを使用 _のみ_ 複数のロケールがある場合：

- `standard` – すべてのパッケージのすべての静的ビューファイルをデプロイします。
- `quick` – （_default_）を使用すると、デプロイメント時間を最小限に抑えることができます。
- `compact`- サーバー上のディスク領域を節約します。 Adobe Commerce バージョン 2.2.4 以前では、この設定はの値よりも優先されます `scd_threads` 値： `1`.

```yaml
stage:
  build:
    SCD_STRATEGY: "compact"
```

## `SCD_THREADS`

- **デフォルト** – 自動
- **バージョン**—Adobe Commerce 2.1.4 以降

静的コンテンツのデプロイメントのスレッド数を設定します。 デフォルト値は検出された CPU スレッド数に基づいて設定され、4 を超えることはありません。 スレッド数を増やすと、静的コンテンツのデプロイメントが高速化されます。スレッド数を減らすと、速度が低下します。 スレッドの値は、次のように設定できます。

```yaml
stage:
  build:
    SCD_THREADS: 2
```

デプロイメント時間をさらに短縮するには、を使用します [設定の管理](../store/store-settings.md) （を使用） `scd-dump` 静的デプロイメントをビルドフェーズに移動するコマンド。

## `SCD_USE_BALER`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.3.0 以降

[ベーラー](https://github.com/magento/baler) 生成された JavaScript コードをスキャンし、最適化された JavaScript バンドルを作成します。 最適化されたバンドルをサイトにデプロイすると、サイトを読み込む際のネットワークリクエストの数を減らし、ページの読み込み時間を短縮できます。

をに設定 `true` 静的コンテンツデプロイメントの実行後に Baler を実行する場合。

```yaml
stage:
  build:
    SCD_USE_BALER: true
```

>[!NOTE]
>
>Baler はアルファリリースなので、実稼動環境で使用することはお勧めしません。

## `SKIP_COMPOSER_DUMP_AUTOLOAD`

- **デフォルト**— _未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

をに設定 `true` をスキップする `composer dump-autoload` cloud Docker のインストール中にコマンドを実行します。 この変数は、書き込み可能なファイルシステムを持つ Cloud Docker コンテナにのみ関連します。 このような場合は、コマンドをスキップすると、削除されたからコードにアクセスしようとする他のコマンドによるエラーを防ぐことができます `generated` ディレクトリ。

Adobe Commerceの実行時 `composer dump-autoload`で生成されたクラスへのリンクを含む自動読み込みファイルが `generated` 読み取り専用ファイルシステムを使用した実稼動環境では問題にならないフォルダー。 ただし、書き込み可能なファイルシステムを使用した Cloud Docker インストールの場合（を使用したテストおよび開発用にのみ作成） `./vendor/bin/ece-docker build:compose --with-test`）、を実行できます `bin/magento -n setup:upgrade` を使用しないコマンド `--keep-generated` オプションを選択すると、 `generated` ディレクトリ。 ディレクトリを削除すると、 `composer dump-autoload` 削除されたディレクトリ内のファイルへのリンクがオートロードに含まれているため、コマンドは失敗します。

```yaml
stage:
  build:
    SKIP_COMPOSER_DUMP_AUTOLOAD: true
```

## `SKIP_SCD`

- **デフォルト**— _未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

をに設定 `true` ビルド段階での静的コンテンツのデプロイメントをスキップする場合。

を使用したビルドフェーズで既に静的コンテンツをデプロイしている場合 [設定の管理](../store/store-settings.md)静的コンテンツのデプロイメントをスキップして、クイックビルドテストを行うことができます。

ビルドフェーズで、を設定します `SKIP_SCD: false` そのため、静的コンテンツのビルドは、プロセスがサイトのデプロイメントに影響を与えず、不要なサイトのダウンタイムも引き起こさないビルドフェーズで行われます。 参照： [静的コンテンツデプロイメント](../deploy/static-content.md).

```yaml
stage:
  build:
    SKIP_SCD: false
```

## `VERBOSE_COMMANDS`

- **デフォルト**—_未設定_
- **バージョン**—Adobe Commerce 2.1.4 以降

を有効または無効にする [交感](https://symfony.com/doc/current/console/verbosity.html) デバッグの詳細レベル： `bin/magento` デプロイメント段階で実行される CLI コマンド。

>[!NOTE]
>
>VERBOSE_COMMANDS を使用して、成功と失敗の両方のコマンド出力の詳細を制御するには、次の手順に従います `bin/magento` CLI コマンド、以下を設定する必要があります [MIN_LOGGING_LEVEL](variables-global.md#minlogginglevel) `debug`.

ログに表示される詳細レベルを選択します。

- `-v`=標準出力
- `-vv`=より詳細な出力
- `-vvv` =デバッグに最適な詳細出力

```yaml
stage:
  build:
    VERBOSE_COMMANDS: "-vv"
```
