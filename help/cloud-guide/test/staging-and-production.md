---
title: ステージングと実稼動のテスト
description: ステージング環境と実稼動環境でのテスト方法について説明します。
exl-id: 5b762d59-04c5-4e89-a637-719141759158
source-git-commit: 1253d8357fd2554050d1775fefbc420a2097db5f
workflow-type: tm+mt
source-wordcount: '1329'
ht-degree: 0%

---

# ステージングと実稼動のテスト

コード、ファイル、データのステージングまたは実稼動環境への移行が正常に完了したら、環境 URL を使用してサイトとストアをテストします。 ログの検証、Fastly 設定のテスト、ユーザー受け入れテスト (UAT) などに関する情報を以下に示します。

## ログファイル

デプロイメントでエラーが発生した場合や、テスト時にその他の問題が発生した場合は、ログファイルを確認してください。 ログファイルは、 `var/log` ディレクトリ。

デプロイログがにある `/var/log/platform/<prodject-ID>/deploy.log`. の値 `<project-ID>` プロジェクト ID と、環境が「ステージング」か「実稼動」かに応じて異なります。 例えば、のプロジェクト ID がの場合、 `yw1unoukjcawe`の場合、ステージングユーザーは `yw1unoukjcawe_stg` 実稼動ユーザーは `yw1unoukjcawe`.

実稼動環境またはステージング環境でログにアクセスする場合は、SSH を使用して 3 つの各ノードにログインし、ログを見つけます。 または、 [New Relic Log Management](../monitor/log-management.md) をクリックして、すべてのノードから集計されたログデータを表示し、クエリを実行します。 詳しくは、 [ログを表示](log-locations.md#application-logs).

## コードベースを確認する

コードベースがステージング環境と実稼動環境に正しくデプロイされていることを確認します。 環境のコードベースは同じにする必要があります。

## 設定を確認

管理パネルで、ベース URL、ベース管理 URL、マルチサイト設定などの設定を確認します。 追加の変更を加える必要がある場合は、ローカル Git ブランチの編集を完了し、 `master` ブランチ（統合、ステージング、実稼動）。

## Fastly キャッシュを確認します。

[Fastly の設定](../cdn/fastly-configuration.md) の詳細には注意が必要です。正しい Fastly サービス ID と Fastly API トークン資格情報の使用、Fastly VCL コードのアップロード、DNS 設定の更新、お使いの環境への SSL/TLS 証明書の適用などです。 これらのセットアップタスクを完了したら、ステージング環境と実稼動環境で Fastly キャッシュを検証できます。

**Fastly サービス設定を検証するには**:

1. で URL を使用して、ステージング用および実稼動用の管理者にログインします。 `/admin`、または [更新された管理 URL](../environment/variables-admin.md#admin-url).

1. に移動します。 **ストア** > **設定** > **設定** > **詳細** > **システム**. スクロールしてクリック **フルページキャッシュ**.

1. 次の点を確認します。 **キャッシュアプリケーション** の値は次の値に設定されます。 _Fastly CDN_ .

1. Fastly の資格情報をテストします。

   - クリック **Fastly 設定**.

   - Fastly サービス ID および Fastly API トークンの資格情報の値を確認します。 詳しくは、 [Fastly 認証情報の取得](/help/cloud-guide/cdn/fastly-configuration.md#get-fastly-credentials).

   - クリック **認証情報のテスト**.

   >[!WARNING]
   >
   >ステージング環境と実稼動環境で正しい Fastly サービス ID と API トークンを入力していることを確認してください。 Fastly 資格情報は、サービス環境ごとに作成およびマッピングされます。 実稼動環境にステージング資格情報を入力すると、VCL スニペットをアップロードできず、キャッシュが正しく機能せず、キャッシュ設定が誤ったサーバーとストアを指すようになります。

**Fastly キャッシュ動作を確認するには**:

1. を使用してヘッダーを確認する `dig` コマンドラインユーティリティを使用して、サイト構成に関する情報を取得します。

   任意の URL を `dig` コマンドを使用します。 以下の例では Pro URL を使用しています。

   - ステージング： `dig https://mcstaging.<your-domain>.com`
   - 実稼動： `dig https://mcprod.<your-domain>.com`

   追加の `dig` テストについては、 Fastly の [DNS を変更する前のテスト](https://docs.fastly.com/en/guides/working-with-domains).

1. 用途 `cURL` をクリックして、応答ヘッダー情報を検証します。

   ```bash
   curl https://mcstaging.<your-domain>.com -H "host: mcstaging.<your-domain.com>" -k -vo /dev/null -H Fastly-Debug:1
   ```

   詳しくは、 [応答ヘッダーを確認する](../cdn/fastly-troubleshooting.md#check-cache-hit-and-miss-response-headers) を参照してください。

1. ライブになったら、 `cURL` をクリックして、ライブサイトを確認します。

   ```bash
   curl https://<your-domain> -k -vo /dev/null -H Fastly-Debug:1
   ```

## UAT テストの完了

ステージング環境および実稼動環境でのユーザー受け入れテスト (UAT) を完了します。 以下のテストは、マーチャントおよびお客様としてテストする可能性のあるタスクと領域のクイックリストです。 リストが長くなり、カスタムモジュール、拡張機能、サードパーティ統合の追加テストが含まれる場合があります。 テスト時には、デスクトップ、ノートパソコン、およびモバイルデバイスを使用します。

問題が発生した場合は、再生手順、エラーメッセージ、不明な画面キャプチャ、リンクを保存します。 この情報を使用して、統合環境のコードと設定または環境設定の問題を調べ、修正します。

<table>
<tr>
<td style="width:150px">ユーザー管理</td>
<td>
<ul>
<li>顧客アカウントの作成と編集、メールの検証</li>
<li>マーチャント向け管理者ロールの作成</li>
<li>特定のロールを持つマーチャントアカウントの作成</li>
<li>ロールごとのマーチャントアカウントアクセスのテスト</li>
</ul>
</td>
</tr>
<tr>
<td>カタログと製品</td>
<td>
<ul>
<li>関連商品を含むカタログの作成</li>
<li>シンプル、設定可能、バンドルされたすべての製品タイプを含む、ストアフロント用の製品を作成します。</li>
<li>製品画像、スウォッチ、ビデオ、その他のメディアオプションの追加</li>
<li>価格、割引、価格ルールの設定 </li>
<li>価格範囲、主な製品、提供日などの高度な機能を設定します。</li>
<li>在庫を変更し、増加および完了した購入ごとに正しい値が表示され、変更されることを確認します。</li>
</ul>
</td>
</tr>
<tr>
<td>買い物かごとチェックアウト</td>
<td>
<ul>
<li>製品を検索し、フィルターオプションを選択します。</li>
<li>検索結果、カテゴリページ、製品ページから買い物かごに製品を追加</li>
<li>すべての製品タイプをテスト</li>
<li>金額を削除または変更して、買い物かごを表示し、内容を変更する </li>
<li>チェックアウトを実行し、買い物かごと製品情報に対する注文額を確認します。</li>
<li>買い物かごに対する税金が正しく計算されたことを確認します。</li>
<li>クーポンの追加、配送料の選択、配送料と請求に関する情報の入力、支払いに関する情報など、様々なオプションで購入を完了します。</li>
<li>チェックアウト時に支払いゲートウェイとオプションを確認</li>
<li>画面上の通知、顧客アカウントに記載されている注文、電子メール通知を確認します。</li>
<li>ゲストおよび顧客のチェックアウトのテスト</li>
</ul>
</td>
</tr>
<tr>
<td>Order Management</td>
<td>
<ul>
<li>顧客の注文の作成</li>
<li>注文の検索と表示</li>
<li>製品の追加と削除、金額の変更、発送および請求情報の変更によるオーダーの変更</li>
<li>払い戻しを処理する</li>
<li>注文のキャンセル</li>
<li>クーポンコードと割引の適用</li>
</ul>
</td>
</tr>
<tr>
<td>サイトコンテンツ</td>
<td>
<ul>
<li>すべてのテーマとアセットが正しく読み込まれていることを確認する</li>
<li>レスポンシブメディアサイズを含め、CSS が正しく表示されることを確認します。</li>
<li>利用条件、返金方針、その他のポリシー情報を確認します。</li>
<li>貴社の連絡先情報、リンクなどを確認する</li>
<li>製品とコンテンツを検索し、結果のフィルタリングを確認します。</li>
<li>フッターブロックと上部ナビゲーションブロックの確認</li>
<li>404 ページとメンテナンスページをテストする</li>
</ul>
</td>
</tr>
<tr>
<td>拡張機能</td>
<td>
<ul>
<li>特に課税、送料、支払いモジュールに関するすべての拡張設定を確認します（例：倉庫および財務管理システムに送信される注文）。</li>
<li>すべてのカスタマイズされたモジュールとインストールされた拡張機能のインタラクションのテスト</li>
<li>完了する必要があるインタラクション（支払い、注文、E メール通知）のデータを確認します。</li>
<li>拡張機能の環境ごとの設定を確認する</li>
<li>モジュールと拡張機能の間の依存関係の検証</li>
<li>商人および顧客としてのすべてのアクションを確認します</li>
</ul>
</td>
</tr>
<tr>
<td>サードパーティ統合</td>
<td>
<ul>
<li>データがAdobe Commerceで正しく保存され、サードパーティのサービスによってエクスポート、プッシュ、アクセスできることを確認します（例：オーダーはサードパーティのオーダー管理システムで表示されます）。</li>
<li>統合ごとの設定とインタラクションの検証</li>
<li>Adobe Commerceとサードパーティのサービスを起点とした往復テストの実施</li>
<li>認証が完了したことを確認する</li>
<li>ログに記録された問題を確認して、コードの統合やコントロールパネルのエラーメッセージを更新します。</li>
</ul>
</td>
</tr>
<tr>
<td>バックエンドのテスト</td>
<td>
<ul>
<li>キャッシュのテストとクリア </li>
<li>再インデックスの実行と結果の検証</li>
<li>cron ジョブを確認し、cron_schedule エラーがないか確認します。</li>
<li>シェルスクリプトの問題を確認し、確認します。</li>
<li>ログに記録された問題を確認します。アプリケーションログ、PHP ログ、MySQL ログ、電子メールログ</li>
</ul>
</td>
</tr>
</table>

## 負荷および応力テスト

開始する前に、ステージング環境と実稼動環境で広範なトラフィックとパフォーマンスのテストを実行することをお勧めします。 フロントエンドプロセスとバックエンドプロセスのパフォーマンステストを検討します。

テストを開始する前に、テストする環境、使用しているツール、および時間枠に関するサポートを含むチケットを入力します。 パフォーマンスを追跡するために、結果と情報でチケットを更新します。 テストを完了したら、更新した結果を追加し、チケットのテストに記録されたメモに日付とタイムスタンプを追加します。

以下を確認します。 [Performance Toolkit](https://github.com/magento/magento2/tree/2.4/setup/performance-toolkit) オプションを使用できます。

最も良い結果を得るには、次のツールを使用します。

- [アプリケーションのパフォーマンステスト](../environment/variables-post-deploy.md#ttfb_tested_pages) — アプリケーションのパフォーマンスをテストするには、 `TTFB_TESTED_PAGES` 環境変数を使用して、サイトの応答時間をテストします。
- [攻城](https://www.joedog.org/siege-home/) — ストアを制限に押し込むためのトラフィックシェーピングとテストソフトウェア。 設定可能な数のシミュレーションされたクライアントを使用してサイトにヒットします。 Siege は、基本的な認証、cookie、HTTP、HTTPS、FTP プロトコルをサポートします。
- [Jmeter](https://jmeter.apache.org) — フラッシュセールスなど、スパイクトラフィックのパフォーマンスを測定するのに役立つ優れた負荷テストです。 サイトに対して実行するカスタムテストを作成します。
- [New Relic](../monitor/new-relic-service.md) （提供） — データの送信、クエリ、Redis など、アクションごとの追跡された時間を使用して、パフォーマンスが低下する原因となるサイトのプロセスや領域を特定するのに役立ちます。
- [WebPageTest](https://www.webpagetest.org) および [Pingdom](https://www.pingdom.com) — サイトページのリアルタイム分析で、異なる接触チャネルの場所で読み込み時間を計測します。 Pingdom には料金が必要な場合があります。 WebPageTest は無料のツールです。

## 機能テスト

Magento機能テストフレームワーク (MFTF) を使用して、Cloud Docker 環境からAdobe Commerceの機能テストを完了できます。 詳しくは、 [アプリケーションのテスト](https://developer.adobe.com/commerce/cloud-tools/docker/test/application-testing/) （内） _Cloud Docker for Commerce ガイド_.

## セキュリティスキャンツールの設定

お使いのサイトには無料のセキュリティスキャンツールがあります。 サイトを追加してツールを実行するには、 [セキュリティスキャンツール](../launch/overview.md#set-up-the-security-scan-tool).