# page-snippet について

## 1.概要

page-snippetは、モック画面を一から作るときに簡単に作成できるようにするコピペ元です。  
また、 `_page_snippet` フォルダの横にある `_snipet` フォルダにあるものは、page-snippetの上で使われる前提で作られています。

## 2.画面パターン

現在、page-snippetは

- ポータルパターン
- 入力パターン

を提供しています。


## 3.構成

### 3-1.HTML

HTMLは大別して以下の2つに分かれます。

- フレームHTML
ページ全体のレイアウトを整える役割を担う。  
（ポータルの）サイドメニュー以外は具体的なものを持たない。  
このフレームの中身をシングルページアプリケーションチックに切り替えていく。  
フレームから別のフレームに遷移するときはURLが替わる想定。  
`_page_snippet` フォルダ直下にある `portal-snippet.html` などがこれにあたる。  

- コンテンツHTML
Ajaxであとから差し込まれるHTML。  
`_page_snippet/template` フォルダ以下にあるHTML（headers.html以外）がこれにあたる。

- ヘッダーHTML
ヘッダーが定義されているHTML。  
`_page_snippet/template` フォルダ以下の `headers.html` がこれにあたる。  
ヘッダーは複数の画面において同じものを使いまわすことが多いため、ヘッダーだけでまとめてある。


### 3-2.json

アプリケーションに必要なjsonを置いておくフォルダです。  
必ず必要なファイルはありません。  
`_page_snippet/json` フォルダ以下に置いてあるjsonはすべてコンテンツHTMLにバインドするためのデータです。  
これらのjsonは次で紹介する `screen-switcher.js` によってコンテンツHTMLにバインドされてページが表示されます。


### 3-3.Javascript

`_page_snippet` に含まれるJavascriptは以下のようなものがあります。

- app.js
HTMLとの関係性においては、フレームHTMLと対になる。  
`_page_snippet` ではポータルパターンと入力パターンの2つのページで同じ `app.js` を使っているが、  
別々のものを使っていても問題ない。（むしろ別々のもの使用するのを推奨）

- ページコンテナ系js
`portal-content1.js` , `input.js` , `confirm.js` , `finish.js` がこれにあたる。  
HTMLとの関係性においては、コンテンツHTMLと対になる。  
シングルページアプリケーション的にページの中身が変わったあと、そのページでどんな動作をするのかを司る。  
ひとつのページ内でさまざまな動きをするシナリオの場合には、ここのソースが太りやすい。

- screen-switcher.js
シングルページアプリケーションチックな動きを実現している部分。  
切り替える先のHTMLとjsonを取得し、データをバインドしてアニメーション付きで表示させる役割。  
いろいろなページに切り替えて動かすシナリオの場合には、ここのソースが太りやすい。

- resource-loader.js
Ajaxでリソース(HTML,json)を取得してくるHTML。  
promiseを返すので、 `setTimeout` を使わずに取得後に処理を直列化して書ける。

- lazyload.js
javascriptの読み込みを遅らせて、DOMのロードを速くするためのもの。  
基本的には `app.js` 以外の自分の書いたソースはこの中の `scripts` に入れて問題ない。
Chromeのdeveloper-toolでは、あとから追加されたscriptタグは最初からは表示されないので、  
デバッグするときには1クッション必要。（関数名を直接consoleに入力して、表示された関数をクリックすると、その関数のあるファイルが開かれる）

- animation-fx.js
フェードインアウトなどのアニメーションを担う部分。
screen-switcher.jsでシングルページアプリケーションっぽく見せるために作られたものだが、  
ある程度融通が利くようには作られているのでCSSの相性が悪くなければどこでも使える。

- side-menu.js
サイドメニューの動きを実現している部分。  
ただ、やっていることはクリックされたら色を付けてイベントを発火しているだけ。  
ポータルパターンじゃない場合はいらない。


### 3-4.CSS
`page-snipet.css` と `animation-fx.css` のどちらも使用します。  
特記事項は特になし。


## 4.使い方

まず、 `_page_snippet` フォルダをコピーして、あなたの作るコンセプトのフォルダ名に書き換えましょう。  
そのあと、適宜ファイル名を変えたり、不要なファイルを削除しましょう。  
以上です。  
  
このフォルダ以下は完全にあなたの世界なので、好き勝手やり放題です！！


## 5.page-snippetのメリット

1. コピーしてくるソース量が少なく、機能追加・修正がしやすい
2. 構文をGoogle Closureに寄せているので、jQueryを知らなくてもGoogle Closureを使ったことがあれば馴染みやすい
2. HTMLテンプレートにデータをバインドしてくるところが隠されているので、HTMLのパスとjsonのパスを指定するだけでいい
3. HTMLやjsonが分離しているので、コンフリクトの可能性が少なくなり作業分担が容易になる
4. デザインレビューの指摘を反映してあるので、整ったベースが作れる
5. Javascriptのlazy-loadの機構が標準搭載されている

