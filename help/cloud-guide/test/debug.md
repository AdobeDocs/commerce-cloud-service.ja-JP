---
title: 設定 [!DNL Xdebug]
description: クラウドインフラストラクチャプロジェクト開発上でAdobe Commerceをデバッグするための Xdebug 拡張機能の設定方法について説明します。
exl-id: bf2d32d8-fab7-439e-8df3-b039e53009d4
source-git-commit: 751456f50e7b017b47c2ff43e008c2d04a558d96
workflow-type: tm+mt
source-wordcount: '1747'
ht-degree: 0%

---

# Xdebug の設定

[!DNL Xdebug] は、PHP をデバッグするための拡張機能です。 任意の IDE を使用できますが、次の手順では、を構成する方法を説明します [!DNL Xdebug] および [!DNL PhpStorm] を追加して、ローカル環境でデバッグを実行します。

>[!NOTE]
>
>次の項目を設定できます。 [!DNL Xdebug] :Cloud Docker 環境で実行してローカルデバッグをおこなう場合に、クラウドインフラストラクチャのAdobe Commerceプロジェクトの設定を変更する必要はありません。 詳しくは、 [Docker 用の Xdebug の設定](https://developer.adobe.com/commerce/cloud-tools/docker/test/configure-xdebug/).

有効にするには [!DNL Xdebug]を設定する場合は、Git リポジトリでファイルを設定し、IDE を設定し、ポート転送を設定する必要があります。 一部の設定は、 `magento.app.yaml` ファイル。 編集後、すべてのスターター環境と Pro 統合環境にわたって Git の変更をプッシュし、を有効にします。 [!DNL Xdebug]. [!DNL Xdebug] は、既に Pro ステージング環境および実稼動環境で使用できます。

設定が完了すると、CLI コマンド、Web リクエスト、コードをデバッグできます。 すべてのクラウドインフラストラクチャ環境は読み取り専用です。 デバッグを実行するには、コードをローカル開発環境に複製します。 Pro ステージング環境と実稼動環境については、 [その他の手順](#debug-for-pro-staging-and-production) 対象： [!DNL Xdebug].

## 要件

を実行して使用するには [!DNL Xdebug]に値を指定する場合は、その環境の SSH URL が必要です。 情報は、 [[!DNL Cloud Console]](../project/overview.md) または [!DNL Cloud Onboarding UI].

## Xdebug の設定

を設定するには、以下を実行します。 [!DNL Xdebug]を使用する場合は、次の手順に従います。

- [ブランチで作業してファイルの更新をプッシュ](#get-started-with-a-branch)
- [有効にする [!DNL Xdebug] （環境の場合）](#enable-xdebug-in-your-environment)
- [IDE の設定](#configure-phpstorm)
- [ポート転送の設定](#set-up-port-forwarding)

### ブランチの概要

追加するには [!DNL Xdebug]を使用する場合、Adobeは、 [開発支店](../dev-tools/cloud-cli-overview.md#create-an-environment-branch).

### お使いの環境での Xdebug の有効化

次を有効にすることができます。 [!DNL Xdebug] を、すべてのスターター環境と Pro 統合環境に直接追加できます。 この設定手順は、実稼動環境およびステージング環境では必要ありません。 詳しくは、 [Pro のステージング環境および実稼動環境でのデバッグ](#debug-for-pro-staging-and-production).

有効にするには [!DNL Xdebug] プロジェクトに対して、 `xdebug` から `runtime:extensions` のセクション `.magento.app.yaml` ファイル。

**Xdebug を有効にするには**:

1. ローカルターミナルで、 `.magento.app.yaml` ファイルを編集します。

1. Adobe Analytics の `runtime` セクション、の下 `extensions`を追加します。 `xdebug`. 例：

   ```yaml
   runtime:
       extensions:
           - redis
           - xsl
           - newrelic
           - sodium
           - xdebug
   ```

1. 変更を `.magento.app.yaml` ファイルを開き、テキストエディタを終了します。

1. 変更を追加、コミット、プッシュして、環境を再デプロイします。

   ```bash
   git add -A
   ```

   ```bash
   git commit -m "Add xdebug"
   ```

   ```bash
   git push origin <environment-ID>
   ```

スターター環境および Pro 統合環境にデプロイする場合、 [!DNL Xdebug] が利用できるようになりました。 IDE の構成を続けます。 PhpStorm の場合は、 [PhpStorm の設定](#configure-phpstorm).

### PhpStorm の設定

The [PhpStorm](https://www.jetbrains.com/phpstorm/) IDE が正しく機能するように構成する必要があります [!DNL Xdebug].

**Xdebug と連携するように PhpStorm を設定するには**:

1. PhpStorm プロジェクトで、 **設定** パネル。

   - _macOS_ — 選択 **PhpStorm** > **環境設定**.
   - _Windows/Linux_ — 選択 **ファイル** > **設定**.

1. Adobe Analytics の _設定_ パネル、展開して **言語とフレームワーク** > **PHP** > **サーバー** 」セクションに入力します。

1. 次をクリック： **+** をクリックして、サーバー設定を追加します。 プロジェクト名は上部に灰色で表示されます。

1. [オプション] 新しいサーバー構成に対して、次の設定を行います。 詳しくは、 [デバッグサーバーが設定されていません](https://www.jetbrains.com/help/phpstorm/troubleshooting-php-debugging.html#no-debug-server-is-configured) （内） _PHPStorm_ ドキュメント。

   - **名前** — ホスト名と同じを入力します。 この値は、 `PHP_IDE_CONFIG` 変数 [Debug CLI コマンド](#debug-cli-commands) ：デバッグに CLI を使用します。
   - **ホスト** — ホスト名を入力します。
   - **ポート** — 入力 `443`.
   - **デバッガー** — 選択 `Xdebug`.

1. 選択 **パスマッピングを使用**. Adobe Analytics の _ファイル/ディレクトリ_ ウィンドウ、 `serverName` が表示されます。

1. Adobe Analytics の **サーバー上の絶対パス** 列で、 **編集** アイコンをクリックし、環境に基づいて設定を追加します。

   - すべてのスターター環境と Pro 統合環境で、リモートパスは `/app`.
   - Pro ステージング環境および実稼動環境の場合：

      - 実稼動： `/app/<project_code>/`
      - ステージング：  `/app/<project_code>_stg/`

1. 次を変更： [!DNL Xdebug] の 9000 番に **言語とフレームワーク** > **PHP** > **デバッグ** > **Xdebug** > **デバッグポート** パネル。

1. クリック **適用**.

### ポート転送の設定

マッピング `XDEBUG` サーバーからローカルシステムへの接続。 任意の種類のデバッグを実行するには、クラウドインフラストラクチャサーバー上のAdobe Commerceからローカルマシンにポート 9000 を転送する必要があります。 次のセクションのいずれかを参照してください。

- [Macまたは UNIX でのポート転送](#port-forwarding-on-mac-or-unix)
- [Windows でのポート転送](#port-forwarding-on-windows)

#### Macまたは UNIX®でのポート転送

**Macまたは UNIX®環境でポート転送を設定するには**:

1. ターミナルを開きます。

1. SSH を使用して接続を確立します。

   ```bash
   ssh -R 9000:localhost:9000 <ssh url>
   ```

   以下を使用します。 `-v` (verbose) オプションを使用して、ソケットが転送されるポートに接続されたときに、そのソケットが端末に表示されます。

   「unable to connect」または「could not listen to port on remote」というエラーが表示された場合は、ポート 9000 を占有しているサーバー上に、別のアクティブな SSH セッションが存続している可能性があります。 その接続が使用されていない場合は、接続を終了できます。

**接続のトラブルシューティングをおこなうには**:

1. SSH を使用して、リモート統合、ステージング、または実稼動環境にログインします。

1. SSH セッションのリストを表示します。 `who`

1. ユーザー別の既存の SSH セッションを表示します。 自分以外のユーザーに影響を与えないように注意してください。

   - 統合：ユーザー名は `dd2q5ct7mhgus`
   - ステージング：ユーザー名は、 `dd2q5ct7mhgus_stg`
   - 実稼動：ユーザー名は `dd2q5ct7mhgus`

1. ユーザーセッションの方が古い場合は、次のような擬似端末 (PTS) 値を見つけます。 `pts/0`.

1. PTS 値に対応するプロセス ID(PID) を強制終了します。

   ```bash
   ps aux | grep ssh
   kill <PID>
   ```

   レスポンスのサンプル：

   ```terminal
   dd2q5ct7mhgus        5504  0.0  0.0  82612  3664 ?      S    18:45   0:00 sshd: dd2q5ct7mhgus@pts/0
   ```

   接続を終了するには、プロセス ID(PID) を指定して kill コマンドを入力します。

   ```bash
   kill 3664
   ```

#### Windows でのポート転送

Windows でポート転送（SSH トンネリング）を設定するには、Windows ターミナルアプリケーションを構成する必要があります。 この例では、 [パテ](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Cygwin などの他のアプリケーションを使用できます。 その他のアプリケーションの詳細については、それらのアプリケーションに付属するベンダーのドキュメントを参照してください。

**Putty を使用して Windows で SSH トンネルを設定するには**:

1. まだおこなっていない場合は、をダウンロードします。 [パテ](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Putty を起動します。

1. [ カテゴリ ] ウィンドウで、 **セッション**.

1. 次の情報を入力します。

   - **ホスト名（または IP アドレス）** フィールド： [SSH URL](../development/secure-connections.md#connect-to-a-remote-environment) クラウドサーバーの
   - **ポート** フィールド：を入力します。 `22`

   ![パテを設定](../../assets/xdebug/putty-session.png)

1. Adobe Analytics の _カテゴリ_ ウィンドウ枠で、 **接続** > **SSH** > **トンネル**.

1. 次の情報を入力します。

   - **ソースポート** フィールド：を入力します。 `9000`
   - **宛先** フィールド：を入力します。 `127.0.0.1:9000`
   - クリック **リモート**

1. クリック **追加**.

   ![Putty での SSH トンネルの作成](../../assets/xdebug/putty-tunnels.png)

1. Adobe Analytics の _カテゴリ_ ウィンドウ枠で、 **セッション**.

1. Adobe Analytics の **保存済みセッション** 「 」フィールドに、この SSH トンネルの名前を入力します。

1. クリック **保存**.

   ![SSH トンネルを保存します。](../../assets/xdebug/putty-session-save.png)

1. SSH トンネルをテストするには、 **読み込み**&#x200B;を選択し、次に **開く**.

   「接続できません」というエラーが表示される場合は、次の点を確認してください。

   - すべての Putty 設定が正しい
   - クラウドインフラストラクチャのプライベートAdobe Commerce SSH キーが配置されているマシンで Putty を実行している

## Xdebug 環境への SSH アクセス

デバッグを開始したり、セットアップを実行したりするには、環境にアクセスするための SSH コマンドが必要です。 この情報は、 [[!DNL Cloud Console]](../development/secure-connections.md#use-an-ssh-command) とプロジェクトのスプレッドシートが含まれます。

スターター環境および Pro 統合環境では、次の機能を使用できます。 `magento-cloud` CLI コマンドを使用して、次の環境に SSH で接続します。

```bash
magento-cloud environment:ssh --pipe -e <environment-ID>
```

次を使用するには： [!DNL Xdebug]、次のように環境に SSH を設定します。

```bash
ssh -R <xdebug listen port>:<host>:<xdebug listen port> <SSH-URL>
```

以下に例を挙げます。

```bash
ssh -R 9000:localhost:9000 pwga8A0bhuk7o-mybranch@ssh.us.magentosite.cloud
```

## Pro のステージング環境および実稼動環境でのデバッグ

>[!NOTE]
>
>Pro のステージング環境および実稼動環境では、 [!DNL Xdebug] は常に使用可能です。これらの環境は、 [!DNL Xdebug]. 通常の Web リクエストはすべて、 [!DNL Xdebug]. したがって、これらのリクエストは通常どおりに処理され、 [!DNL Xdebug] が読み込まれます。 Web リクエストが送信されたとき、 [!DNL Xdebug] キーを渡すと、PHP プロセスにルーティングされ、 [!DNL Xdebug] 読み込み済み。

次を使用するには： [!DNL Xdebug] 特に、Pro プランのステージング環境と実稼動環境では、アクセス権のある SSH トンネルと Web セッションを個別に作成します。 この使用方法は、一般的なアクセスとは異なります。すべてのユーザーにはアクセスできず、ユーザーにはアクセスできません。

以下が必要です。

- 環境にアクセスするための SSH コマンド。 この情報は、 [[!DNL Cloud Console]](../project/overview.md) または [!DNL Cloud Onboarding UI].
- The `xdebug_key` の値は、ステージング環境と Pro 環境を設定する際に設定します。

  The `xdebug_key` は、SSH を使用してプライマリノードにログインし、次のコマンドを実行すると見つかります。

  ```bash
  cat /etc/platform/*/nginx.conf | grep xdebug.sock | head -n1
  ```

**ステージング環境または実稼動環境への SSH トンネルを設定するには**:

1. ターミナルを開きます。

1. クラスターの各 Web ノードに対するすべての SSH セッションをクリーンアップします。

   ```bash
   ssh USERNAME@CLUSTER.ent.magento.cloud 'rm /run/platform/USERNAME/xdebug.sock'
   ```

1. クラスターの各 Web ノードに対して、Xdebug 用の SSH トンネルを設定します。

   ```bash
   ssh -R /run/platform/USERNAME/xdebug.sock:localhost:9000 -N USERNAME@CLUSTER.ent.magento.cloud
   ```

**環境 URL を使用してデバッグを開始するには**:

1. リモートデバッグを有効にします。ブラウザーのサイトにアクセスし、次の URL を次の URL に追加します。 `KEY` 次の値の `xdebug_key`.

   ```http
   ?XDEBUG_SESSION_START=KEY
   ```

   この手順では、ブラウザーリクエストをトリガーに送信する Cookie を設定します [!DNL Xdebug].

1. でデバッグを完了 [!DNL Xdebug].

1. セッションを終了する準備が整ったら、次のコマンドを使用して、Cookie を削除し、次の場所にあるブラウザーを介したデバッグを終了します。 `KEY` 次の値の `xdebug_key`.

   ```http
   ?XDEBUG_SESSION_STOP=KEY
   ```

   >[!NOTE]
   >
   >The `XDEBUG_SESSION_START` 通り過ぎる `POST` リクエストはサポートされていません。

## Debug CLI コマンド

このセクションでは、CLI コマンドのデバッグの手順を説明します。

CLI コマンドをデバッグするには：

1. CLI コマンドを使用して、デバッグするサーバーに SSH で接続します。

1. 次の環境変数を作成します。

   ```bash
   export XDEBUG_CONFIG='PHPSTORM'
   ```

   ```bash
   export PHP_IDE_CONFIG="serverName=<name of the server that is configured in PHPSTORM>"
   ```

   これらの変数は、SSH セッションが終了すると削除されます。

1. デバッグを開始

   スターター環境と Pro 統合環境で、CLI コマンドを実行してデバッグします。
ランタイムオプションを追加できます。例：

   ```bash
   php -d xdebug.profiler_enable=On -d xdebug.max_nesting_level=9999 bin/magento cache:clean
   ```

   Pro ステージング環境および実稼動環境では、 [!DNL Xdebug] CLI コマンドをデバッグする際の PHP 設定ファイル。次に例を示します。

   ```bash
   php -c /etc/platform/USERNAME/php.xdebug.ini bin/magento cache:clean
   ```

## Web リクエストのデバッグ

次の手順は、Web リクエストのデバッグに役立ちます。

1. 次の日： _拡張_ メニュー、クリック **デバッグ** を有効にします。

1. 右クリックし、オプションメニューを選択し、IDE キーをに設定します。 **PHPSTORM**.

1. をインストールします。 [!DNL Xdebug] クライアントがブラウザーに表示されます。 設定して有効にします。

### 例：Chrome の設定

このセクションでは、 [!DNL Xdebug] Chrome で、 [!DNL Xdebug] ヘルパー拡張機能。 詳しくは、 [!DNL Xdebug] 他のブラウザーのツールについては、ブラウザーのドキュメントを参照してください。

**Chrome で Xdebug ヘルパーを使用するには**:

1. の作成 [SSH トンネル](#ssh-access-to-xdebug-environments) をクラウドサーバーに追加します。

1. をインストールします。 [Xdebug Helper 拡張機能](https://chromewebstore.google.com/detail/eadndfjplgieldjbigjakmdgkmoaaaoc) を Chrome ストアから削除します。

1. 次の図に示すように、Chrome で拡張機能を有効にします。

   ![Chrome での Xdebug 拡張機能の有効化](../../assets/xdebug/enable-chrome-ext.png)

1. Chrome で、Chrome ツールバーの緑のヘルパーアイコンを右クリックします。

1. ポップアップメニューで、 **オプション**.

1. 次から： _IDE キー_ リスト、クリック **PhpStorm**.

1. クリック **保存**.

   ![Xdebug ヘルパーオプション](../../assets/xdebug/helper-options.png)

1. PhpStorm プロジェクトを開きます。

1. 上部のナビゲーションバーで、 **リスニングを開始** アイコン。

   ナビゲーションバーが表示されない場合は、 **表示** > **ナビゲーションバー**.

1. PhpStorm ナビゲーションウィンドウで、テストする PHP ファイルをダブルクリックします。

## ローカルコードのデバッグ

読み取り専用の環境なので、デバッグを実行するには、環境または特定の Git ブランチからローカルワークステーションにコードをプルする必要があります。

選択する方法は自由です。 次のオプションがあります。

- Git からコードをチェックアウトし、を実行します。 `composer install`

  この方法は `composer.json` は、アクセス権のないプライベートリポジトリ内のパッケージを参照します。 このメソッドを使用すると、Adobe Commerceのコードベース全体が取得されます。

- をコピーします。 `vendor`, `app`, `pub`, `lib`、および `setup` ディレクトリ

  この方法を使用すると、テスト可能なすべてのコードがに含まれます。 静的アセットの数によっては、大量のファイルを含む転送が長くなる場合があります。

- をコピーします。 `vendor` ディレクトリのみ

  ほとんどのコードは `vendor` ディレクトリ内で、このメソッドを使用すると、適切なテストがおこなわれる可能性が高くなりますが、はコードベース全体をテストするわけではありません。

**ファイルを圧縮してローカルマシンにコピーするには**:

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
