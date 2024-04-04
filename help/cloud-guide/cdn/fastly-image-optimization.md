---
title: 画像の最適化
description: Fastly 画像の最適化を有効にして設定することで、Adobe Commerceサイトの画像配信を最適化し、画像管理をシンプルにする方法について説明します。
feature: Cloud, Configuration, Media
exl-id: 20f5c5c3-7c43-493b-b651-82b023cf5db5
source-git-commit: 7a181af2149eef7bfaed4dd4d256b8fa19ae1dda
workflow-type: tm+mt
source-wordcount: '1275'
ht-degree: 0%

---

# 画像の最適化

Fastly 画像の最適化 (Fastly IO) は、リアルタイムの画像操作と最適化を提供し、画像の配信を高速化し、レスポンシブ Web アプリケーション用の画像ソースセットのメンテナンスを簡素化します。 設定が完了すると、Fastly IO は次の画像最適化機能を提供します。

- 非可逆変換を強制
- ディープ画像の最適化
- 適応的なピクセル比
- 一般的な画像形式 (PNG、JPEG、GIF、WebP) のサポート

Fastly IO オプションを有効にして設定する前に、Fastly サービスを設定し、Origin シールドを設定する必要があります。

設定に基づいて、 Fastly 画像の最適化 (Fastly IO) スニペットが VCL コードを挿入し、ストアフロントでの製品画像の配信を高速化する画像の最適化を実行します。 Fastly IO を設定する手順は、有効化、設定、検証の 3 つです。

## Fastly IO を有効にする

Fastly IO VCL スニペットをアップロードして、管理パネルから Fastly 画像の最適化 (Fastly IO) を有効にします。 このスニペットでは、デフォルトの設定を使用して、画像の最適化を通じてすべての画像を処理するための Fastly 設定手順を提供します。

**前提条件：**

- Fastly モジュールバージョン 1.2.62 以降のインストールまたはアップグレード
- [Fastly Origin シールドとバックエンドを設定する](fastly-custom-cache-configuration.md#configure-back-ends-and-origin-shielding)

**Fastly IO を有効にするには**:

1. ローカルにログイン [管理者](../../get-started/onboarding.md#access-your-admin-panel) 管理者としてパネルを使用します。

1. 選択 **ストア** > **設定** > **設定** > **詳細** > **システム**.

1. 右側のウィンドウで、を展開します。 **フルページキャッシュ**.

1. 選択 **Fastly 設定** > **画像の最適化** をクリックして設定を指定します。

1. Adobe Analytics の _Fastly IO スニペット_ フィールド、選択 **有効/無効**.

1. Fastly IO スニペットをアップロードします。

   - 選択 **デフォルトの I/O 設定オプション** をクリックして、画像最適化のデフォルト設定オプションページを開きます。
   - 選択 **アップロード** をクリックして、VCL スニペットをサーバーにアップロードします。

## Fastly IO を設定

必要に応じて、画像の最適化用のデフォルトの IO 設定を確認し、更新します。 例えば、非可逆形式用に WebP とJPEGの品質レベルを変更したり、JPEG画像を次の形式で配信する場合に、 _プログレッシブ_ または _ベースライン_. また、Fastly I/O を使用して、以下のようなより詳細な画像最適化機能を実現できます。

- 非可逆変換を強制
- ディープ画像の最適化
- 適応的なピクセル比

**Fastly IO を更新するには**:

1. 次の日： _Fastly 設定_ ページの _デフォルトの I/O 設定オプション_ フィールド、選択 **設定**.

   ![Fastly I/O の設定を表示します。](../../assets/cdn/fastly-io-default-config.png)

1. の Fastly I/O 設定を確認し、更新します。 _画像の最適化のデフォルト設定オプション_ ページ：

   ![Fastly IO 設定を確認する](../../assets/cdn/fastly-io-config-options.png)

   - **自動 WebP** — デフォルト設定 (`Yes`) をクリックして、画像をサポートしているブラウザーの WebP 形式に画像を変換します。 設定を **いいえ**&#x200B;の場合、画像を WebP 形式に変換する代わりに、画像ファイル形式を使用することになります。

   - **デフォルトの WebP（非可逆）画質** — デフォルト設定 (`85`) または非可逆ファイル形式の画像の圧縮レベルを入力します。 1 ～ 100 の任意の整数を指定できます。

   - **デフォルトのJPEG形式コントロール**  — デフォルト設定 (`Auto`) または画像を提供する際に使用するJPEGの種類を選択します。 値が _自動_&#x200B;を使用すると、入力タイプと一致する出力タイプで画像を配信できます。 選択 _ベースライン_ を指定すると、画像が左上から右下に向かって線分で表示されます。 選択 _プログレッシブ_ 読み込み時に明確になるぼやけた画像を表示する。

   - **デフォルトのJPEG品質** — デフォルト設定 (`85`) または非可逆ファイル形式の品質の圧縮レベルを入力します。 1 ～ 100 の任意の整数を指定します。

   - **拡大を許可しますか？** — デフォルト設定 (`No`) またはを選択します。 `Yes` を指定すると、元のソースファイルよりも大きい画像が返され、要求されたサイズに収まるようになります。

   - **フィルターのサイズ変更** — デフォルト設定 (`Lancsoz3`) をクリックするか、別のオプションを選択します。 この設定は、サイズ変更された画像を配信する際に使用するフィルターを指定します。 選択したフィルターに応じて、サイズ変更された画像のピクセル数が多い場合と少ない場合があります。

      - `Lanczos3` （デフォルト） — 最高品質のイメージを配信します。 画像内のエッジや線形の特徴を検出する機能が向上し、 _[!DNL sinc]_再サンプリングを行い、可能な限り最適な再構築を提供します。
      - `Lanczos2` — と同じフィルタを使用します。 `Lancsoz3` しかし、 _[!DNL sinc]_再サンプリング機能
      - `Bicubic` — 画像を小さくするときに、自然なシャープ効果があります。
      - `Bilinear` — イメージを大きくするときに、自然なスムージング効果を持ちます。
      - `Nearest` — ピクセルアートのサイズを変更するときに自然なピクセル化効果が得られます。

1. Fastly サービスの IO 構成設定を指定した後、 **キャンセル** をクリックして、Fastly 設定に戻ります。

1. 画像の最適化設定の場合 _ディープ画像の最適化を有効にする_ フィールド、選択 **はい** ディープ画像の最適化をオンにします。

   ![Fastly I/O ディープ画像の最適化を有効にする](../../assets/cdn/fastly-io-deep-image-config.png)

   ディープ画像の最適化は、デフォルトでオフになっています。 この機能を有効にすると、Adobe Commerceの組み込みのサイズ変更機能がオフになり、サイズ変更作業が Fastly IO サービスにオフロードされます。 画像の最適化は、製品画像にのみ適用されます。 CMS 画像はサイズ変更されません。 詳しくは、 [Fastly ドキュメント](#deep-image-optimization).

1. ディープ画像の最適化を有効にした後、 [適応ピクセル比](#adaptive-pixel-ratios) レスポンシブ web サイトで使用するように最適化された画像を生成する機能です。

   ![Fastly IO アダプティブピクセル比を有効にする](../../assets/cdn/fastly-io-config-adaptive-pixel.png)

   - Adobe Analytics の _アダプティブデバイスのピクセル比を有効にする_ フィールド、選択 **はい**.
   - Adobe Analytics の _デバイスのピクセル比_ フィールドを選択するか、デフォルト設定を受け入れるか、 **システム入力** チェックボックスをオンにして設定を削除します。 次に、目的の比率を選択します。 「デバイスのピクセル比」設定を高くすると、より大きな画像を配信します。

1. 選択 **設定を保存**.

### 非可逆変換を強制

デフォルトでは、Fastly IO サービスは、PNG、BMP、WEBP などのロスレス形式をJPEG/WEBP 形式に変換します。

非可逆変換を強制的に実行する利点は、小さい画像が提供されることです。
例えば、PNG の代わりにJPEGまたは WEBp 形式を使用すると、Fastly IO 設定で指定された品質レベルに応じて、サイズが 60～70%小さくなる場合があります。

画像の最適化で選択した品質レベルに応じて、画像の視覚的な違いが認識される場合があります。 例えば、Alphaの背景色を使用するディープ画像の最適化を使用しない限り、テーマのチャンネルや透明度は削除され、白の背景に置き換えられます。

可逆変換 (`WebP Auto? = No`) の場合、Fastly IO は、互換性のあるブラウザーに対してのみJPEG画像を WEBP 形式に変更します。 他の画像タイプは変更されません。 例えば、元の画像が PNG の場合、Fastly I/O サービスからの出力は PNG になります。

### ディープ画像の最適化

ディープ画像の最適化は、デフォルトでオフになっています。 このオプションを有効にすると、組み込みのAdobe Commerceのサイズ変更がオフになり、Fastly IO サービスに完全にオフロードされます。
この機能は、サイズ変更のみをおこないます _製品_ 画像。 CMS 画像はサイズ変更されません。

ディープ画像の最適化を有効にすると、テーマで定義されたすべての画像に背景色の定義が追加されます。 その結果、WebP 画像は WebP 可逆から WebP 可逆に切り替えられる。 非可逆と非可逆の主な違いの 1 つは、非可逆性では PNG 画像からアルファチャンネルをドロップするので、画像がはるかに小さくなります。 ただし、透明度を持つ画像は、異なる背景を使用する製品ページやキャンペーンページで不適切に見える場合があります。

例えば、次のコードは Luma テーマの画像の元のソースを表しています。

```html
<img class="product-image-photo"
     src="https://mymagentosite/pub/media/catalog/product/cache/f073062f50e48eb0f0998593e568d857/m/b/mb02-gray-0.jpg"
     width="240"
     height="300"
     alt="Fusion Backpack"/>
```

Fastly I/O ディープ画像最適化機能が有効な場合、次の例に示すように、画像の元のソースコードが書き換えられます。

```html
<img class="product-image-photo"
     src="https://mymagentosite/pub/media/catalog/product/m/b/mb02-gray-0.jpg?width=240&height=300&quality=80&bg-color=255,255,255&fit=bounds"
     width="240"
     height="300"
     alt="Fusion Backpack"/>
```

### 適応的なピクセル比

アダプティブピクセル比機能は、プログレッシブ Web アプリケーション用の画像を最適化する場合に役立ちます。 1 つの画像ソースファイルから複数の画像サイズと解像度を配信する場合は、 `srcset` 各製品画像に対して

アダプティブピクセル比機能を有効にすると、Fastly IO サービスは固定幅の画像を配信し、様々な画像に適応できます `device-pixel-ratios`.
例えば、サービスは次の例に示すように、製品の画像定義を変更します。

```html
<img class="product-image-photo"
     srcset="https://mymagentosite/pub/media/catalog/product/m/b/mb02-gray-0.jpg?width=240&height=300&quality=80&bg-color=255,255,255&fit=bounds&dpr=2 2x,
  https://mymagentosite/pub/media/catalog/product/m/b/mb02-gray-0.jpg?width=240&height=300&quality=80&bg-color=255,255,255&fit=bounds&dpr=3 3x"
     src="https://mymagentosite/pub/media/catalog/product/m/b/mb02-gray-0.jpg?width=240&height=300&quality=80&bg-color=255,255,255&fit=bounds"
     width="240"
     height="300"
     alt="Fusion Backpack"/>
```

詳しくは、 `srcset` [ブラウザーのサポート](https://caniuse.com/#feat=srcset) および [仕様](https://html.spec.whatwg.org/multipage/embedded-content.html#attr-img-srcset).

## Fastly IO を検証

Fastly IO を有効にして設定した後、Fastly IO が有効になっているかどうかに関わらず、Web ページの速度テストを実行して、設定を検証します。 また、ストア内の画像を確認して、画像のサイズと外観に問題がないかを確認します。
