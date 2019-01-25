# ESモジュールを使ったコードをIE11向けに変換する方法
こちらは「[JavaScript コードレシピ集：｜技術評論社](https://gihyo.jp/book/2019/978-4-297-10368-2/support)」サポートページの一部です。

**⭐サンプルコードや説明は定期的に更新されています。学習の際は常に最新のものをご利用ください。⭐**

ESモジュールを使ったコードを、Internet Explorer 11（以下、IE11）で動作させるための考え方や変換方法について紹介しています。書籍内では19章269においてESモジュールを使っていますので、その変換を通して解説します。

なお、IE11対応の基本的な考え方・新構文の対応方法・新機能の対応方法については以下のリポジトリをご使用ください。

- [本書のサンプルコードをInternet Explorer 11で動作させるための手順](https://github.com/ics-creative/js-code-recipe-ie11)

## ダウンロード
サンプルファイルを一括でダウンロードする場合は次のURLを参照してください。

https://github.com/ics-creative/js-code-recipe-ie11-es-module/archive/master.zip

## フォルダー構成について

本リポジトリーのサンプルコードは以下のフォルダー構成になっています。

```
js-code-recipe-ie11-es-modules
　├ samples：変換対象のサンプルコード
　└ transpiled_samples：IE11で動作するサンプルコード
```

※ `sample` フォルダーのサンプルコードは通常サンプルをベースにしていますが、IE11対応用に一部変更しています（後述）。

## ESモジュールのIE11向け変換の考え方

ESモジュールの構文については、Babelとポリフィルだけでは不十分です。ESモジュールをIE11向けでも動作するよう変換するには、モジュールバンドラーというツールが必要です。代表的なものについては、次のようなものがあります。

- [webpack](https://ics.media/entry/12140)
- [Parcel](https://parceljs.org/)

今回は設定ファイルが少なくて済むParcelでの設定方法について解説します。Parcelを使うことで、**ESモジュールの`import`と`export`を使った構文が1つのJavaScriptファイルにバンドル（複数ファイルが1つのファイルにまとまること）されます**。Parcel内ではBabelを使っているので、BabelによるIE11向けの構文変換を同時に行えるため、結果的にESモジュールを用いたコードがIE11でも動作する形に変換されます。

変換後のコードはIE11だけではなくモダンブラウザーでも動作するので、IE11対応が必要なプロジェクトに利用するとよいでしょう。

なお、webpackを用いても同様のことができます。webpackによる設定方法については、ICS MEDIAの記事「[最新版で学ぶwebpack入門](https://ics.media/entry/12140)」を参照ください。

### 環境準備
1. コマンドラインツールを起動します。

2. コマンドで本フォルダーに移動します。

▼ Windowsでのフォルダーの移動のコマンド

```bash
cd C:¥Users¥★★★js¥code-recipe-ie11-es-module
```

▼ macOSでのフォルダーの移動のコマンド

```bash
cd /Users/★★★/js-code-recipe-ie11-es-module
```

3. コマンドで、変換に必要なファイルをインストールします。

```bash
npm install
```

### HTMLコードの更新：ポリフィルを読み込む

次の手順と同じです。

- [サンプルコードをIE11で動作させる方法 - ポリフィルの読み込み](https://github.com/ics-creative/js-code-recipe-ie11#html%E3%82%B3%E3%83%BC%E3%83%89%E3%81%A7%E3%83%9D%E3%83%AA%E3%83%95%E3%82%A3%E3%83%AB%E3%82%92%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%82%80)

### HTMLコードの更新：`script`タグの`type="module"`を削除する
19章269では、ESモジュールを使うために`script`タグに対して`type="module"`を設定しています。

- [該当箇所のソースコード](https://github.com/ics-creative/js-code-recipe/blob/master/samples/c19/269/index.html#L8)

今回の変換では各モジュールを1つのファイルにまとめるので、この指定を行うべきではありません。また、指定するとIE11でエラーになります。削除しておきましょう。

```html
<!-- モジュールバンドラーを使うので、scriptタグからtype="module"を削除した -->
<script src="main.js" defer></script>
```

- [GitHub上で確認する](https://github.com/ics-creative/js-code-recipe-ie11-es-module/blob/master/samples/c19/269/index.html#L8-L9)

### JavaScriptのコードを変換する

次のコマンドを実行します。

```bash
npm run build
```

`samples`フォルダー内のJavaScriptが変換されます。変換後のコードはHTMLやCSSファイルとともに`transpiled_samples`フォルダーへ出力されます。

### 変換するファイルを変更したい場合

今回紹介した変換スクリプトは、`package.json`にスクリプト内容が記述されています。次のようなフォルダー構成に対応しています。

- 変換対象フォルダー名：`src`
- メインとなるJavaScriptファイル名：`main.js`
- 出力フォルダー名：`output`

メインとなるJavaScriptファイル名とは、HTMLコードの`<script type="module"`></script>で読み込んでいるJavaScriptのことです。19章269では、[main.jsを読み込んでいます](https://github.com/ics-creative/js-code-recipe/blob/19c182b4d6b94286ebe4f179dba6f672775ce8ae/samples/c19/269/index.html#L8)。

変換するファイル名を変更するには、`package.json` ファイルの`"config"`の箇所を書き換えます。

```json
"config": {
  "src": "変換フォルダーへのパス",
  "main_js_file": "メインとなるJavaScriptファイル名",
  "output": "出力フォルダーへのパス"
},
```

### IE11で動作チェック
IE11でも動作が確認しやすいように、次のURLで変換後のサンプルを公開しています。

- [c19/269をIE11向けに変換したサンプル](https://ics-creative.github.io/js-code-recipe-ie11-es-module/transpiled_samples/c19/269/index.html)

▼ IE11で動作している様子

![IE11での動作](https://user-images.githubusercontent.com/7123759/51656025-adc5c480-1fe2-11e9-9d35-f188e0b952c2.png)
