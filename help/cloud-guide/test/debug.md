---
title: 設定 [!DNL Xdebug]
description: クラウドインフラストラクチャプロジェクト開発でAdobe Commerceをデバッグするための Xdebug 拡張機能を設定する方法について説明します。
exl-id: bf2d32d8-fab7-439e-8df3-b039e53009d4
source-git-commit: 751456f50e7b017b47c2ff43e008c2d04a558d96
workflow-type: tm+mt
source-wordcount: '1747'
ht-degree: 0%

---

# Xdebug の設定

[!DNL Xdebug] は、PHP をデバッグするための拡張モジュールです。 任意の IDE を使用できますが、次に設定方法を説明します [!DNL Xdebug] および [!DNL PhpStorm] ローカル環境でデバッグする場合は、をクリックします。

>[!NOTE]
>
>以下を設定できます [!DNL Xdebug] を Cloud Docker 環境で実行し、クラウドインフラストラクチャプロジェクト設定のAdobe Commerceを変更せずにローカルデバッグを行う。 参照： [Docker 用の Xdebug の設定](https://developer.adobe.com/commerce/cloud-tools/docker/test/configure-xdebug/).

を有効にする [!DNL Xdebug]は、Git リポジトリにファイルを設定し、IDE を設定し、ポート転送を設定する必要があります。 一部の設定は、 `magento.app.yaml` ファイル。 編集後、すべてのスターター環境と Pro 統合環境にわたって Git の変更をプッシュして、有効にします [!DNL Xdebug]. [!DNL Xdebug] は、ステージング環境および実稼動環境で既に使用可能です。

設定が完了すると、CLI コマンド、Web リクエストおよびコードをデバッグできるようになります。 すべてのクラウドインフラストラクチャ環境は読み取り専用であることに注意してください。 デバッグを実行するために、ローカル開発環境にコードを複製します。 ステージング環境と実稼動環境については、を参照してください。 [追加手順](#debug-for-pro-staging-and-production) （用） [!DNL Xdebug].

## 要件

を実行して使用するには [!DNL Xdebug]。環境の SSH URL が必要です。 次の方法で情報を特定できます [[!DNL Cloud Console]](../project/overview.md) または [!DNL Cloud Onboarding UI].

## Xdebug の設定

を設定 [!DNL Xdebug]は、次の手順に従います。

- [ブランチでの作業によるファイル更新のプッシュ](#get-started-with-a-branch)
- [Enable （有効） [!DNL Xdebug] 環境の場合](#enable-xdebug-in-your-environment)
- [IDE の設定](#configure-phpstorm)
- [ポート転送の設定](#set-up-port-forwarding)

### ブランチの基本を学ぶ

追加 [!DNL Xdebug]、Adobeでは、で作業することをお勧めします [開発部門](../dev-tools/cloud-cli-overview.md#create-an-environment-branch).

### お使いの環境で Xdebug を有効にする

を有効にできます [!DNL Xdebug] すべてのスターター環境および Pro 統合環境に直接適用できます。 この設定手順は、実稼動環境とステージング環境には必要ありません。 参照： [ステージング環境および実稼動環境用のデバッグ](#debug-for-pro-staging-and-production).

を有効にする [!DNL Xdebug] プロジェクトに次を追加します `xdebug` に `runtime:extensions` の節 `.magento.app.yaml` ファイル。

**Xdebug を有効にするには**:

1. ローカルターミナルで、を開きます `.magento.app.yaml` ファイルをテキストエディターで開きます。

1. が含まれる `runtime` セクション、 `extensions`、追加： `xdebug`. 例：

   ```yaml
   runtime:
       extensions:
           - redis
           - xsl
           - newrelic
           - sodium
           - xdebug
   ```

1. 変更をに保存します。 `.magento.app.yaml` ファイルを開き、テキストエディターを終了します。

1. 変更を追加、コミットおよびプッシュして、環境を再デプロイします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Add xdebug"
   ```

   ```bash
   git push origin <environment-ID>
   ```

スターター環境と Pro 統合環境にデプロイした場合、 [!DNL Xdebug] が利用可能になりました。 IDE の設定を続行します。 PhpStorm については、を参照してください。 [PhpStorm の設定](#configure-phpstorm).

### PhpStorm の設定

この [PhpStorm](https://www.jetbrains.com/phpstorm/) IDE がと正しく連携するように設定する必要があります [!DNL Xdebug].

**Xdebug と連携するように PhpStorm を設定するには**:

1. PhpStorm プロジェクトで、 **設定** パネル。

   - _macOS_ – 選択 **PhpStorm** > **環境設定**.
   - _Windows/Linux_ – 選択 **ファイル** > **設定**.

1. が含まれる _設定_ パネルで、を展開して見つけます。 **言語とフレームワーク** > **PHP** > **サーバー** セクション。

1. 「」をクリックします **+** サーバー設定を追加します。 プロジェクト名は、上部がグレーで表示されます。

1. [オプション] 新しいサーバー設定に次の設定を行います。 参照： [デバッグサーバーが設定されていません](https://www.jetbrains.com/help/phpstorm/troubleshooting-php-debugging.html#no-debug-server-is-configured) が含まれる _PHPStorm_ ドキュメント。

   - **名前**— ホスト名と同じ値を入力します。 この値は、の値と一致する必要があります `PHP_IDE_CONFIG` 変数 [Debug CLI コマンド](#debug-cli-commands) をクリックします。
   - **ホスト**— ホスト名を入力します。
   - **ポート**—Enter `443`.
   - **デバッガー** – 選択 `Xdebug`.

1. を選択 **パスマッピングの使用**. が含まれる _ファイル/ディレクトリ_ ペインに変更します。ここでは、 `serverName` が表示されます。

1. が含まれる **サーバーの絶対パス** 列で、 **編集** アイコンをクリックし、環境に基づいて設定を追加してください。

   - すべてのスターター環境および Pro 統合環境の場合、リモートパスは `/app`.
   - ステージング環境および実稼動環境の場合：

      - 実稼動： `/app/<project_code>/`
      - ステージング：  `/app/<project_code>_stg/`

1. 変更： [!DNL Xdebug] の 9000 への移植 **言語とフレームワーク** > **PHP** > **デバッグ** > **Xdebug** > **デバッグポート** パネル。

1. クリック **適用**.

### ポート転送の設定

をマッピングします `XDEBUG` サーバーからローカルシステムへの接続。 あらゆる種類のデバッグを行うには、Cloud Infrastructure Server 上のAdobe Commerceからローカルマシンにポート 9000 を転送する必要があります。 以下のセクションの 1 つを参照してください。

- [Macまたは UNIX のポート転送](#port-forwarding-on-mac-or-unix)
- [Windows でのポート転送](#port-forwarding-on-windows)

#### Macまたは UNIX のポート転送®

**Macまたは UNIX® 環境でポート転送を設定するには：**:

1. ターミナルを開きます。

1. SSH を使用して接続を確立します。

   ```bash
   ssh -R 9000:localhost:9000 <ssh url>
   ```

   の使用 `-v` （verbose） オプションを指定すると、転送されるポートにソケットが接続されるたびに、端末に表示されます。

   「接続できません」または「リモートのポートをリッスンできませんでした」というエラーが表示される場合は、ポート 9000 を占有しているサーバー上に、別のアクティブな SSH セッションが存在している可能性があります。 その接続が使用されていない場合は、終了できます。

**接続のトラブルシューティングをおこなうには**:

1. SSH を使用して、リモート統合、ステージング、または実稼動環境にログインします。

1. SSH セッションのリストを表示します。 `who`

1. ユーザー別の既存の SSH セッションを表示します。 自分以外のユーザーに影響を与えないように注意してください。

   - 統合：ユーザー名は次に似ています `dd2q5ct7mhgus`
   - ステージング：ユーザー名は次に似ています `dd2q5ct7mhgus_stg`
   - 実稼働：ユーザー名は次に似ています `dd2q5ct7mhgus`

1. ユーザーセッションが以前の場合、次のような擬似端末（PTS）値を見つけます。 `pts/0`.

1. PTS 値に対応するプロセス ID （PID）を強制終了します。

   ```bash
   ps aux | grep ssh
   kill <PID>
   ```

   応答の例：

   ```terminal
   dd2q5ct7mhgus        5504  0.0  0.0  82612  3664 ?      S    18:45   0:00 sshd: dd2q5ct7mhgus@pts/0
   ```

   接続を終了するには、プロセス ID （PID）を指定して kill コマンドを入力します。

   ```bash
   kill 3664
   ```

#### Windows でのポート転送

Windows にポート転送（SSH トンネリング）をセットアップするには、Windows ターミナル アプリケーションを構成する必要があります。 この例では、を使用して SSH トンネルを作成する手順を示します [パテ](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Cygwin などの他のアプリケーションを使用できます。 その他のアプリケーションについて詳しくは、それらのアプリケーションに付属するベンダードキュメントを参照してください。

**Putty を使用して Windows に SSH トンネルをセットアップするには**:

1. まだ行っていない場合は、次をダウンロードします [パテ](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. パテを起動します。

1. カテゴリ ウィンドウで、 **Session**.

1. 次の情報を入力します。

   - **ホスト名（または IP アドレス）** フィールド： [SSH URL](../development/secure-connections.md#connect-to-a-remote-environment) クラウドサーバーの場合
   - **ポート** フィールド：Enter `22`

   ![パテの設定](../../assets/xdebug/putty-session.png)

1. が含まれる _カテゴリ_ ウィンドウで、をクリック **接続** > **SSH** > **トンネル**.

1. 次の情報を入力します。

   - **ソースポート** フィールド：Enter `9000`
   - **宛先** フィールド：Enter `127.0.0.1:9000`
   - クリック **リモート**

1. クリック **追加**.

   ![Putty で SSH トンネルを作成する](../../assets/xdebug/putty-tunnels.png)

1. が含まれる _カテゴリ_ ウィンドウで、をクリック **Session**.

1. が含まれる **保存済みセッション** フィールドに、この SSH トンネルの名前を入力します。

1. クリック **保存**.

   ![SSH トンネルを保存する](../../assets/xdebug/putty-session-save.png)

1. SSH トンネルをテストするには、 **ロード**&#x200B;を選択し、 **開く**.

   「接続できません」というエラーが表示される場合は、次の点を確認してください。

   - Putty の設定はすべて正しい
   - クラウドインフラストラクチャ上のプライベートAdobe Commerceの SSH キーがあるマシンで Putty を実行しています

## Xdebug 環境への SSH アクセス

デバッグの開始やセットアップの実行などを行うには、環境にアクセスするための SSH コマンドが必要です。 この情報は、 [[!DNL Cloud Console]](../development/secure-connections.md#use-an-ssh-command) とプロジェクトのスプレッドシートを使用します。

スターター環境と Pro 統合環境の場合は、次を使用できます `magento-cloud` これらの環境に SSH 接続するための CLI コマンド：

```bash
magento-cloud environment:ssh --pipe -e <environment-ID>
```

使用目的 [!DNL Xdebug]：以下のように環境に SSH で接続します。

```bash
ssh -R <xdebug listen port>:<host>:<xdebug listen port> <SSH-URL>
```

以下に例を挙げます。

```bash
ssh -R 9000:localhost:9000 pwga8A0bhuk7o-mybranch@ssh.us.magentosite.cloud
```

## ステージング環境および実稼動環境用のデバッグ

>[!NOTE]
>
>ステージング環境および実稼動環境では、 [!DNL Xdebug] これらの環境には特別な設定があるので、は常に使用できます。 [!DNL Xdebug]. 通常の Web リクエストはすべて、を持たない専用の PHP プロセスにルーティングされます。 [!DNL Xdebug]. したがって、これらのリクエストは通常どおりに処理され、次の場合にパフォーマンスが低下することはありません。 [!DNL Xdebug] が読み込まれました。 を持つ web リクエストが送信されたとき [!DNL Xdebug] キーを押すと、次のコードを持つ別の PHP プロセスにルーティングされます。 [!DNL Xdebug] 読み込み済み。

使用目的 [!DNL Xdebug] 特に、Pro プランのステージング環境および実稼動環境では、アクセス権のあるユーザーのみ、個別の SSH トンネルと Web セッションを作成します。 この使用方法は、通常のアクセス方法とは異なり、すべてのユーザーではなく、ユーザーにアクセス権を提供するだけです。

以下が必要です。

- 環境にアクセスするための SSH コマンド。 この情報は、 [[!DNL Cloud Console]](../project/overview.md) または [!DNL Cloud Onboarding UI].
- この `xdebug_key` ステージング環境と Pro 環境を設定する際に設定される値。

  この `xdebug_key` は、SSH を使用してプライマリノードにログインし、次のコマンドを実行することで確認できます。

  ```bash
  cat /etc/platform/*/nginx.conf | grep xdebug.sock | head -n1
  ```

**ステージング環境または実稼動環境への SSH トンネルを設定するには**:

1. ターミナルを開きます。

1. クラスターの各 web ノードに対するすべての SSH セッションをクリーンアップします。

   ```bash
   ssh USERNAME@CLUSTER.ent.magento.cloud 'rm /run/platform/USERNAME/xdebug.sock'
   ```

1. クラスターの各 web ノードに対して、Xdebug 用の SSH トンネルを設定します。

   ```bash
   ssh -R /run/platform/USERNAME/xdebug.sock:localhost:9000 -N USERNAME@CLUSTER.ent.magento.cloud
   ```

**環境 URL を使用してデバッグを開始するには：**:

1. リモートデバッグを有効にします。ブラウザーでサイトにアクセスし、URL に次の内容を追加します。この URL は次のとおりです。 `KEY` の値である `xdebug_key`.

   ```http
   ?XDEBUG_SESSION_START=KEY
   ```

   この手順では、ブラウザーリクエストをトリガーに送信する Cookie を設定します [!DNL Xdebug].

1. を使用したデバッグの完了 [!DNL Xdebug].

1. セッションを終了する準備が整ったら、次のコマンドを使用して cookie を削除し、ブラウザーによるデバッグを終了します。このコマンドは次のとおりです。 `KEY` の値である `xdebug_key`.

   ```http
   ?XDEBUG_SESSION_STOP=KEY
   ```

   >[!NOTE]
   >
   >この `XDEBUG_SESSION_START` 渡された `POST` リクエストはサポートされていません。

## Debug CLI コマンド

このセクションでは、CLI コマンドのデバッグについて説明します。

CLI コマンドをデバッグするには：

1. CLI コマンドを使用して、デバッグ対象のサーバに SSH で接続します。

1. 次の環境変数を作成します。

   ```bash
   export XDEBUG_CONFIG='PHPSTORM'
   ```

   ```bash
   export PHP_IDE_CONFIG="serverName=<name of the server that is configured in PHPSTORM>"
   ```

   これらの変数は、SSH セッションが終了すると削除されます。

1. デバッグの開始

   スターター環境と Pro 統合環境では、CLI コマンドを実行してデバッグします。
次のような実行時オプションを追加できます。

   ```bash
   php -d xdebug.profiler_enable=On -d xdebug.max_nesting_level=9999 bin/magento cache:clean
   ```

   ステージング環境および実稼動環境では、へのパスを指定する必要があります [!DNL Xdebug] CLI コマンドのデバッグ時の PHP 設定ファイル。例：

   ```bash
   php -c /etc/platform/USERNAME/php.xdebug.ini bin/magento cache:clean
   ```

## Web リクエストのデバッグ

次の手順は、web リクエストのデバッグに役立ちます。

1. 日 _拡張機能_ メニュー、クリック **デバッグ** を有効にします。

1. 右クリックして「オプション」メニューを選択し、IDE キーをに設定します。 **PHPSTORM**.

1. のインストール [!DNL Xdebug] ブラウザー上のクライアント。 を設定して有効にします。

### 例：Chrome のセットアップ

この節では、の使用方法について説明します [!DNL Xdebug] Chrome でを使用する場合 [!DNL Xdebug] ヘルパー拡張機能。 詳しくは、 [!DNL Xdebug] ツールその他のブラウザーについては、ブラウザーのドキュメントを参照してください。

**Chrome で Xdebug ヘルパーを使用するには**:

1. を作成 [SSH トンネル](#ssh-access-to-xdebug-environments) をクラウドサーバーに送信します。

1. のインストール [Xdebug Helper 拡張機能](https://chromewebstore.google.com/detail/eadndfjplgieldjbigjakmdgkmoaaaoc) Chrome ストアから。

1. Chrome で拡張機能を有効にします（下図を参照）。

   ![Chrome で Xdebug 拡張機能を有効にする](../../assets/xdebug/enable-chrome-ext.png)

1. Chrome では、Chrome ツールバーの緑のヘルパーアイコンを右クリックします。

1. ポップアップメニューから、 **オプション**.

1. から _IDE キー_ リスト、クリック **PhpStorm**.

1. クリック **保存**.

   ![Xdebug Helper オプション](../../assets/xdebug/helper-options.png)

1. PhpStorm プロジェクトを開きます。

1. 上部のナビゲーションバーで、 **リスニングを開始** アイコン。

   ナビゲーション バーが表示されない場合は、 **表示** > **ナビゲーションバー**.

1. PhpStorm のナビゲーションペインで、テストする PHP ファイルをダブルクリックします。

## ローカルコードをデバッグ

読み取り専用環境なので、デバッグを実行するには、環境または特定の Git ブランチからローカルワークステーションにコードをプルする必要があります。

その方法は君次第だ。 以下のオプションがあります。

- Git からコードをチェックアウトして実行する `composer install`

  この方法は、次の場合を除いて機能します `composer.json` アクセス権のないプライベートリポジトリ内のパッケージを参照します。 このメソッドは、Adobe Commerce コードベース全体を取得します。

- をコピーします `vendor`, `app`, `pub`, `lib`、および `setup` ディレクトリ

  この方法を使用すると、テスト可能なすべてのコードを入手できます。 保有する静的アセットの数に応じて、大量のファイルを含む長い転送が必要になる場合があります。

- をコピーします `vendor` ディレクトリのみ

  コードのほとんどは `vendor` ディレクトリの場合、コードベース全体をテストしているわけではありませんが、この方法を使用すると適切なテストが行われる可能性があります。

**ファイルを圧縮してローカル マシンにコピーするには**:

1. SSH を使用してリモート環境にログインします。

1. ファイルを圧縮します。

   ```bash
   tar -czf /tmp/<file-name>.tgz <directory list>
   ```

   例えば、 `vendor` ディレクトリのみ：

   ```bash
   tar -czf /tmp/vendor.tgz vendor
   ```

1. ローカル環境では、PhpStorm を使用してファイルを圧縮します。

   ```bash
   cd <phpstorm project root dir>
   ```

   ```bash
   rsync <SSH-URL>:/tmp/<file-name>.tgz .
   ```

   ```bash
   tar xzf <file-name>.tgz
   ```
