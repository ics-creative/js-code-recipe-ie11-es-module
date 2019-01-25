# ESモジュールを使ったサンプルコードをInternet Explorer 11で動作させる方法

こちらは「[JavaScript コードレシピ集｜技術評論社](https://gihyo.jp/book/2019/978-4-297-10368-2/support)」サポートページの一部です。

**⭐ サンプルコードや説明は定期的に更新されています。学習の際は常に最新のものをご利用ください ⭐**

ESモジュールを使ったコードを、Internet Explorer 11（以下、IE11）で動作させるための考え方や対応方法について紹介しています。19章269「処理ごとにファイルを分割したい（ESモジュール）」がESモジュールのサンプルなので、そちらを例にして解説します。

なお、IE11対応の基本的な考え方・新構文の対応方法・新機能の対応方法については次のURLを参照ください。

- [サンプルコードをInternet Explorer 11で動作させる方法](https://github.com/ics-creative/js-code-recipe-ie11)

## ダウンロード
サンプルファイルを一括でダウンロードする場合は次のURLを参照してください。

https://github.com/ics-creative/js-code-recipe-ie11-es-module/archive/master.zip

## フォルダー構成
サンプルファイルは次のフォルダー構成になっています。

```
js-code-recipe-ie11-es-modules
　├ samples/c19/269：対象のサンプルコード
　└ transpiled_samples/c19/269：IE11に対応済みのサンプルコード
```

※ `sample`フォルダーのサンプルコードは通常サンプルをベースにしていますが、IE11対応用に一部変更しています（後述）。

## 基本的な考え方

ESモジュールの構文について、IE11で動作させるにはBabelとポリフィルだけでは不十分です。ESモジュールの変換にはモジュールバンドラーというツールが必要です。モジュールバンドラーを使うことで、**ESモジュールの`import`と`export`を使った構文が1つのJavaScriptファイルにバンドル（複数ファイルが1つのファイルにまとまること）されます**。代表的なものとして次のものがあります。

- [webpack](https://ics.media/entry/12140)
- [Parcel](https://parceljs.org/)

今回は設定ファイルが少なくて済むParcelを用います。Parcel内ではBabelを使っているので、結果的にESモジュールを用いたコードがIE11でも動作する形に変換（トランスパイル）されます。

対応後のコードはIE11だけではなくモダンブラウザーでも動作するので、IE11対応が必要なプロジェクトに利用するとよいでしょう。

なお、webpackを用いても同様のことができます。webpackによる設定方法については、ICS MEDIAの記事「[最新版で学ぶwebpack入門](https://ics.media/entry/12140)」を参照ください。

## 本書のサンプルコードの対応手順

`transpiled_samples`内にはじめから入っているサンプルは、IE11に対応済みのファイルです。自身でESモジュールのIE11対応を行うときのために、対応手順を紹介します。ステップは2つです。

1. HTMLコード内の`script`タグより`type="module"`を削除する
2. JavaScriptコードの変換とポリフィルの適用

### ステップ1: HTMLコード内の`script`タグより`type="module"`を削除する
サンプルでは、ESモジュールを使うために`script`タグに対して`type="module"`を設定しています。

- [該当箇所のソースコード](https://github.com/ics-creative/js-code-recipe/blob/master/samples/c19/269/index.html#L8)

モジュールバンドラーでは各モジュールを1つのファイルにまとめるので、この指定を行うべきではありません。削除しておきましょう。

```html
<!-- モジュールバンドラーを使うので、scriptタグからtype="module"を削除した -->
<script src="main.js" defer></script>
```

- [GitHub上で確認する](https://github.com/ics-creative/js-code-recipe-ie11-es-module/blob/master/samples/c19/269/index.html#L8-L9)

### ステップ2: JavaScriptコードの変換とポリフィルの適用

JavaScriptコードの変換とポリフィルの適用を自動的に行うツールを準備しました。とくに**面倒な設定は必要なく、数回のコマンドを実行するだけ使えます**。まずはこのツールを利用してIE11対応の手順を学びましょう。

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

3. 次のコマンドで、ツールに依存するファイルをインストールします。

```bash
 npm install
```

### JavaScriptコードの変換とポリフィルの適用

次のコマンドでJavaScriptコードの変換とポリフィルの適用が行われます。

```bash
npm run build
```

各ファイルは`transpiled_samples`フォルダーへ出力されます。

### 対象ファイルを変更したい場合


今回紹介したツールは、`package.json`にスクリプト内容が記述されています。次のようなフォルダー構成に対応しています。

- 対象フォルダー名：`src`
- メインとなるJavaScriptファイル名：`main.js`
- 出力フォルダー名：`output`

メインとなるJavaScriptファイル名とは、HTMLコードの`<script type="module"></script>`で読み込んでいるJavaScriptのことです。サンプルでは[main.jsを読み込んでいます](https://github.com/ics-creative/js-code-recipe/blob/19c182b4d6b94286ebe4f179dba6f672775ce8ae/samples/c19/269/index.html#L8)。

各ファイルを変更するには、`package.json`ファイルの`"config"`の箇所を書き換えます。

```json
（中略）
"config": {
  "src": "対象フォルダーへのパス",
  "main_js_file": "メインとなるJavaScriptファイル名",
  "output": "出力フォルダーへのパス"
},
（中略）
```

### IE11で動作チェック
IE11でも動作が確認しやすいように、次のURLでサンプルを公開しています。

- [c19/269をIE11向けに変換したサンプル](https://ics-creative.github.io/js-code-recipe-ie11-es-module/transpiled_samples/c19/269/index.html)

▼ IE11で動作している様子

![IE11での動作](https://user-images.githubusercontent.com/7123759/51656025-adc5c480-1fe2-11e9-9d35-f188e0b952c2.png)
