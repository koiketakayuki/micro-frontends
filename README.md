
## はじめに

この記事は翻訳記事です。
<a href="https://github.com/naltatis" target="_blank">原著者</a>の許可をとって翻訳・掲載しています。
原文は<a href="https://micro-frontends.org" target="_blank">こちら</a>です。  

# マイクロフロントエンド

モダンなウェブアプリを、異なるjavascriptフレームワークを使う複数のチームで開発するためのテクニック

## マイクロフロントエンドとは？

マイクロフロントエンドという言葉は2016年の終わりに<a href="https://www.thoughtworks.com/radar/techniques/micro-frontends" target="_blank">ThoughtWorks Technology Radar</a>で言及されました。  
それはマイクロサービスの考え方をフロントエンドに拡張したものです。  
現在のWebのトレンドは多機能でパワフルなSPAです。  
SPAはフロントエンドとバックエンドを切り離すという、マイクロサービスの考え方に基づいています。

開発をすすめていくと、特に複数のチームに管理された場合にありがちですが  
フロントエンド層が肥大化して管理が難しくなりがちです。  
これを「モノリシックなフロントエンド」と呼びます。  
  
マイクロフロントエンドの背景にある考えは   
ページを独立したチームによって管理された機能の集合体と捉えることです。  
**それぞれのチームは特定の領域やミッションに特化しています**。  
チームは機能横断的な作りになっていて、その機能をend-to-end  
つまりデータベースからUIに至るまでをすべて実装します。  

この考え方は特に新しいものではありません。  
過去にも<a href="https://dev.otto.de/2014/07/29/scaling-with-microservices-and-vertical-decomposition/" target="_blank">Frontend Integration for Verticalised Systems</a>や<a href="http://scs-architecture.org/" target="_blank">Self-contained Systems</a>といったものがありました。  
しかしマイクロフロントエンドは明らかにそれらの用語よりもライトでとっつきやすい概念です。


##### モノリシックなフロントエンド

![Frontend Monolith](https://micro-frontends-japanese.org/resources/monolith-frontback-microservices.png)

##### マイクロフロントエンド

![Frontend Monolith](https://micro-frontends-japanese.org/resources/verticals-headline.png)

## モダンなWebアプリとは
前文で「モダンなWebアプリ」という言葉を使いましたが、その意味をはっきりさせておきましょう。  

自分のアプリがモダンかどうかの立ち位置を俯瞰的に見るために
<a href="https://ar.al/" target="_blank">Alan Balkan</a>は<a href="https://2018.ar.al/notes/the-documents-to-applications-continuum/" target="_blank">「Documents‐to‐Applications Continuum」</a>という考え方を取り入れました。  
これは左側ほどリンクや文字のような静的なものを、右側ほど写真の編集ツールのような動的なものを配置するというWebサイトの指標です。  
右側に行くほどモダンなWebアプリということになります。  
  
もしあなたのUIがこの指標で左側寄りなら、**サーバーサイドに完結しているのが良いでしょう**。  
サーバサイドで完結する場合、ページはすべてのコンポーネントのHTML文字列を集めて組み合わせるだけです。  
コンテンツの更新はページの再読み込みか、ajaxで行われます。  
<a href="https://twitter.com/gustaf_nk/" target="_blank">Gustaf Nilsson Kotte</a>がこれについて<a href="https://gustafnk.github.io/microservice-websites/" target="_blank">まとめた記事</a>を書いています。  
  
もしあなたのUIがユーザーの動作に対してすぐにフィードバックをしてあげたいなら  
前述のサーバーで完結しているモデルは、不十分です。  
<a href="https://www.smashingmagazine.com/2016/11/true-lies-of-optimistic-user-interfaces/" target="_blank">Optimic UI</a>や
<a href="https://micro-frontends.org/#the-dom-is-the-api">Skelton Screens</a>のようなテクニックを使いたいなら  
UIを**デバイス上で更新する必要があります**。  

また、Googleの
<a href="https://developers.google.com/web/progressive-web-apps/" target="_blank">Progressive Web Apps</a>
では高速なパフォーマンスと  
すべての人にコンテンツを届けることの両立(Progerssive Enhancement)を説明しています。  
このようなアプリケーションは先ほどの指標の真ん中あたりに位置しています。  
これもまたサーバー完結モデルでは不十分で、アプリケーションを**ブラウザと統合**する必要があります。  
これが今記事のテーマです。

## マイクロフロントエンドの根底にある考え

+ テクノロジーとの分離

各チームは実装するのに使う技術を、他のチームとの調整をせずに選ぶべきです。  
CustomElementはその技術の実装を隠して、技術に依存しないインターフェースを提供してくれます。  

+ チームをまたいでコードを共有しない

例え使っている技術が同じでもコードを共有してはいけません。  
それ自身で完結しているアプリケーションを作りましょう。  
共有の状態や、グローバルの変数に依存してはいけません。

+ チームのprefixを決める

チームが上手く分離されていない場合、チームごとに変数やファイルのprefixを決めておきましょう。  
これらが使われたCSS、Event, LocalStorageやCookieは  
どのチームが受け持つべきかをはっきりさせたり、衝突を避けたりするのに役立ちます。

+ ブラウザネイティブのAPIを使う

コンポーネント間のデータのやりとりには、できるだけブラウザネイティブのイベントを使いましょう。  
もしコンポーネント間をつなぐ自作のAPIが必要ならできるだけシンプルにしましょう。  

+ 不測の事態に強い(Risilientな)サイトにする

例えjavascriptが動かなくかったり、途中で止まったりしても、そのサイトは役立つかもしれません。  
体感パフォーマンスを上げるためにサーバーサイドレンダリングやProgressive Enhancementの考え方を取り入れましょう

---

## DOMがAPIになる

Custom Elementはアプリケーションをブラウザに統合するのに役立ちます。  
それぞれのチームは選択したフレームワークを用いて、コンポーネントを実装して  
それをCustom Elementの中に隠蔽します。(例: `<order-minicart></order-minicart>`)  
このタグの名前が他のチームがコンポーネントを使う際のAPIとしての役割を果たします。  
こうすることで、このコンポーネントがどんなライブラリを使って作られたのかを知る必要なく使えます。  
ただDOMとやりとりすればいいのです。

しかし、Custom Elementだけではすべての問題を解決できません。  
Progressive Enhancementやサーバーサイドレンダリング、ルーティングなどを実現するためには  
別のテクノロジーが必要になります。  

以下のページは二つのセクションに分かれています。  
最初にページの組み立て(どのような別々のチームに実装されたコンポーネントを組み合わせるか)について、  
次にクライアントサイドでのページ遷移の例を説明します。

## ページの組み立て

色んなフレームワークで書かれたコードを、クライアントとサーバー両方に統合する以外にも  
javascriptの分離、CSSの衝突の避け方、コンポーネントの遅延読み込み、チーム間のリソース共有、  
データ取得、ローディングなど議論すべきことたくさんがあります。  
これらのトピックを一度にまとめて紹介します。

### プロトタイプ

トラクターの販売店の販売ぺージが以降の説明のモデルになります。  

このページには３つのトラクターを切り替える**セレクター**がついています。  
切り替わるとトラクターの画像、名前、値段、リコメンドが更新されます。  
また、クリックすると選択されているトラクターをバスケットにいれる**購入ボタン**があります。  
さらに上部にはバスケットの状態に応じて表示が切り替わる**ミニバスケット**がついています。  

![Base Protptype](https://micro-frontends-japanese.org/resources/model-store-0.gif)

<a href="https://micro-frontends.org/0-model-store/" target="_blank">ブラウザで試す</a>&<a href="https://github.com/neuland/micro-frontends/tree/master/0-model-store" target="_blank">Github</a>

すべてのHTMLはクライアントサイドでjavascriptとes6のテンプレートを使って生成されていて、ライブラリは使っていません。  
マークアップと状態を分離して、何か変更があった際にはすべてのHTMLを再描画してるだけです。  
DOMの変更分だけを再描画したり、サーバーサイドレンダリングといったものもありません。  
また、タスクをチームごとに分割といったこともしていません。ひとつのjs/cssにすべてが書かれています。

### クライアントサイドとの統合
以下の例では、ページを３つのコンポーネントに分離して、それぞれの実装をチームごとにアサインします。  
**Checkoutチーム(青)** は購入に関するすべてのプロセスを担当します。ここでは購入ボタンとミニバスケットです。  
**Inspireチーム(緑)** はこのページのレコメンドを担当します。  
**Productチーム(赤)** はページ全体を担当します。どの機能が必要で、どこに置くかを決めます。  

![Client-side integration](https://micro-frontends-japanese.org/resources/three-teams.png)

<a href="https://micro-frontends.org/1-composition-client-only/" target="_blank">ブラウザで試す</a>&<a href="https://github.com/neuland/micro-frontends/tree/master/1-composition-client-only" target="_blank">Github</a>


ページはProductチームが決めた商品名や画像などを表示しますが  
それだけでなく他のチームが作ったコンポーネントも含まれます。

#### Custom Elementの作り方

例として購入ボタンを考えてみましょう。  
Productチームは`<blue-buy sku="t_porsche"></blue-buy>`と表示したい位置に書けばページにボタンを追加できます。  
Checkoutチームは`blue-buy`コンポーネントをこのページに登録する必要があります。  

<pre class="highlight"><code>class BlueBuy extends HTMLElement {
  constructor() {
    super();
    this.innerHTML = `&lt;button type="button"&gt;buy for 66,00 €&lt;/button&gt;`;
  }
  disconnectedCallback() { ... }
}
window.customElements.define('blue-buy', BlueBuy);
</code></pre>

ブラウザは`blue-buy`を見つけるたびに上のコードのconstructorを呼び出します。  
`this`は定義されたCustomElement自身への参照を表します。  
`innerHTML`や`getAttributes()`といったDOMのプロパティは、すべて使用可能です。  

![Custom-Element](https://micro-frontends-japanese.org/resources/custom-element.gif)

コンポーネントの名前に関するルールが一つだけあります。  
HTMLの互換性を保つためにコンポーネント名に`-`を含まなければなりません。  
以降の例では`[チームカラー]-[機能名]`という命名規則が用いられます。  
これはチーム間でのコンポーネントの名前衝突を避け、責任を明確にするという目的があります。  

#### 親->子へのデータの受け渡し/DOMの更新

ユーザーがセレクターでトラクターを切り替えたときに  
それに応じて購入ボタンも更新しなければなりません。  

Productチームは単に古いコンポーネントを削除して、新しいものを挿入すれば十分です。

<pre class="highlight"><code>container.innerHTML;
// =&gt; &lt;blue-buy sku="t_porsche"&gt;...&lt;/blue-buy&gt;
container.innerHTML = '&lt;blue-buy sku="t_fendt"&gt;&lt;/blue-buy&gt;';
</code></pre>

削除する際に、古いコンポーネントの`disconnectedCallback`が同期的に呼び出されます。  
その後、新しく作られたコンポーネント(t_fendt)の`constructor`が呼び出されます。

よりパフォーマンスの良い方法は、`sku`プロパティを書き換えることです。

<pre class="highlight"><code>document.querySelector('blue-buy').setAttribute('sku', 't_fendt');
</code></pre>

もしコンポーネント内でReactのようなDOMの変更を検知するフレームワークを使っていた場合は  
内部で自動的に再描画されます。

![Custom-Element rerender](https://micro-frontends-japanese.org/resources/custom-element-attribute.gif)

このプロパティが書き換わった際に再描画される機能を自前で実装する時は  
その挙動を`attributeChangedCallback`に、ウォッチするプロパティを  
`observedAttributes`に定義しておきます。

<pre class="highlight"><code>const prices = {
  t_porsche: '66,00 €',
  t_fendt: '54,00 €',
  t_eicher: '58,00 €',
};

class BlueBuy extends HTMLElement {
  static get observedAttributes() {
    return ['sku'];
  }
  constructor() {
    super();
    this.render();
  }
  render() {
    const sku = this.getAttribute('sku');
    const price = prices[sku];
    this.innerHTML = `&lt;button type="button"&gt;buy for ${price}&lt;/button&gt;`;
  }
  attributeChangedCallback(attr, oldValue, newValue) {
    this.render();
  }
  disconnectedCallback() {...}
}
window.customElements.define('blue-buy', BlueBuy);
</code></pre>

コードの重複を避けるために、`render()`メソッドが定義されてます  
(`constructor`と`attributeChangedCallback`から呼び出されます)。  
このメソッドは再描画に必要な情報を集めてきます。  
Custom Element内でテンプレートエンジンやライブラリを使う場合に  
初期化コードを書くのもこの`render()`内部です。

### ブラウザサポート

上の例で使われているCustom ElementはCustom Element V1 Specに基づいています。  
これはChromeとSafariとOperaでサポートされています。  
ただし`document-register-element` に関して言えば主要ブラウザで動かすためにpolyfillが必要です。  
Custom Element内部では<a href="https://caniuse.com/#feat=mutationobserver" target="_blank">主要ブラウザでサポートされている</a>Mutation Observer APIを使っているため  
裏側でトリッキーなDOMの変更検知をしている、といったことはありません。

### フレームワーク対応
Custom ElementはWeb標準なのでAngular, React, Preact, Vue, Hyperappなど  
主要なjavascriptフレームワークはこれをサポートしています。  
しかし細かい点を見ると、対応できていなかったり、バグがあったりします。  
<a href="https://custom-elements-everywhere.com/" target="_blank">Custom Elements Everywhere Rob Dodson</a>では  
これらの問題をまとめてくれています。

### 子 -> 親への(もしくは兄弟間)のデータのやりとり/DOMイベント

親から子にデータを渡すだけでは不十分です。  
先ほどの例では、購入ボタンを押したときにミニバスケットの表示を  
変更する必要があり、これは親から子へのデータの受け渡しだけでは実現できません。  

この２つのコンポーネントはCheckoutチーム(青)の担当です。  
購入ボタンとミニバスケットを繫ぐAPIを実装することも可能ですが  
その方法では、購入ボタンとミニバスケットが密結合し、コンポーネントの分離原則が破られてしまいます。  

クリーンな方法は、PubSubパターンを使うことです。  
コンポーネントはメッセージを発行し、他のコンポーネントは  
メッセージを(どのコンポーネントから来たかは知らずに)受け取ります。  
ラッキーなことにブラウザには初めからこの機能が組み込まれています。  
`click`, `select`, `mouseover`といったイベントをハンドリングする時と  
全く同じようにこの機能をCustom Elementで使うことができます。  
また` new CustomEvent(...)`とすることで、独自のイベントも使うことが可能です。  
Eventは常にそれが発火された、もしくは渡された場所と紐づいています。  
ほとんどのイベントはbubblingの機能を実装しているため  
特定のDOMのサブツリー内のすべてのイベントを検知するということも可能です。  
以下は`blue:basket:changed`イベントを発火する例です。

<pre class="highlight"><code>class BlueBuy extends HTMLElement {
  [...]
  connectedCallback() {
    [...]
    this.render();
    this.firstChild.addEventListener('click', this.addToCart);
  }
  addToCart() {
    // maybe talk to an api
    this.dispatchEvent(new CustomEvent('blue:basket:changed', {
      bubbles: true,
    }));
  }
  render() {
    this.innerHTML = `&lt;button type="button"&gt;buy&lt;/button&gt;`;
  }
  disconnectedCallback() {
    this.firstChild.removeEventListener('click', this.addToCart);
  }
}
</code></pre>

このミニバスケットは`window`からイベントを購読して  
内部のデータをリフレッシュします。  

<div class="highlight"><pre class="highlight"><code>class BlueBasket extends HTMLElement {
  connectedCallback() {
    [...]
    window.addEventListener('blue:basket:changed', this.refresh);
  }
  refresh() {
    // fetch new data and render it
  }
  disconnectedCallback() {
    window.removeEventListener('blue:basket:changed', this.refresh);
  }
}
</code></pre></div>

ミニバスケットコンポーネントは外部の`window`にリスナーを追加します。  
この方法を気持ち悪いと思うのなら、ページがコンポーネントの変更を検知して  
ミニバスケットの`refresh()`メソッドを呼び出すことで再描画する、という書き方も可能です。  

<pre class="highlight"><code>// page.js
const $ = document.getElementsByTagName;

$('blue-buy')[0].addEventListener('blue:basket:changed', function() {
  $('blue-basket')[0].refresh();
});
</code></pre>

DOMのメソッドを呼び出すということは普通はやりませんが
<a href="https://developer.mozilla.org/de/docs/Web/HTML/Using_HTML5_audio_and_video#Controlling_media_playback" target="_blank">video-elemt api</a>ではよく使われます。  

可能なら宣言的な方法(属性が変わった際に自動で更新される方法)の方が望ましいでしょう。

## サーバーサイドレンダリング

Custom Elementはコンポーネントとブラウザを統合するすばらしい技術ですが初期描画が遅いという欠点があります。  
ユーザーはすべてのjavascriptがロードして実行されるまで、白い画面を見続けることになるでしょう。  
javascriptがロードや実行に失敗したら何が起こるかを考えてみるのもよいでしょう。

<a href="https://adactio.com/" target="_blank">Jeremy Keith</a>が<a href="https://resilientwebdesign.com/" target="_blank">Resilient Web Design</a>でこのことについてまとめています。

よって大事なコンテンツをサーバーサイドでレンダリングすることが大切になるのですが  
悲しいことにweb componentの仕様では、サーバーサイドレンダリングに関する言及はありません。

##  Custom Element + サーバーサイドレンダリング = ❤️

前の例でサーバーサイドレンダリングを有効にするためにはリファクタが必要です。  
各チームはexpressサーバーからコンポーネントを配信します。  
こうすることで、URL経由でコンポーネントの`render()` メソッドを呼び出せます。

<pre class="highlight"><code>$ curl http://127.0.0.1:3000/blue-buy?sku=t_porsche
&lt;button type="button"&gt;buy for 66,00 €&lt;/button&gt;
</code></pre>

Custom Elementのタグの名前はURLのパスとして、属性はGETパラメータとして表現されます。  
この方法は、すべてのコンポーネントをサーバーサイドレンダリングすることが可能で  
**Universal Web Component**にかなり近いものが実現できます。  

<pre class="highlight"><code>&lt;blue-buy sku="t_porsche"&gt;
  &lt;!--#include virtual="/blue-buy?sku=t_porsche" --&gt;
&lt;/blue-buy&gt;
</code></pre>

`#include` コメントは<a href="https://en.wikipedia.org/wiki/Server_Side_Includes" target="_blank">Server Side Includes</a>です。  
これは昔のWebサイトで現在の時間を表示するのに用いられたテクニックと全く同じです。  
他にも色々な方法(
<a href="https://en.wikipedia.org/wiki/Edge_Side_Includes" target="_blank">ESI</a>,
<a href="https://github.com/Schibsted-Tech-Polska/nodesi" target="_blank">nodesi</a>,
<a href="https://github.com/tes/compoxure" target="_blank">compoxure</a>,
<a href="https://github.com/zalando/tailor" target="_blank">tailor</a>)がありますが、SSIが最もシンプルで安定している方法です。  

`#include` コメントはサーバーがページ全体を送信する前に、`/blue-buy?sku=t_porsche` の中身に置き換えられます。  
この時のnginxの設定ファイルは以下のようになります。  

<pre class="highlight"><code>upstream team_blue {
  server team_blue:3001;
}
upstream team_green {
  server team_green:3002;
}
upstream team_red {
  server team_red:3003;
}

server {
  listen 3000;
  ssi on;

  location /blue {
    proxy_pass  http://team_blue;
  }
  location /green {
    proxy_pass  http://team_green;
  }
  location /red {
    proxy_pass  http://team_red;
  }
  location / {
    proxy_pass  http://team_red;
  }
}
</code></pre>

`ssi: on;` の部分でSSIを許可しています。  
また`upstream` と`location` の部分でそれぞれのチームにURLを割り当てています。  
例えば`/blue` で始まるURLは青チームのサーバー(team_blue:3001)にマッピングされます。  
またルートページ(`/`)はページ全体の担当である赤チームのサーバーが割り当てられています。  

以下のアニメーションはjavascriptを無効化した例です。  
![js-diabled](https://micro-frontends-japanese.org/resources/server-render.gif)

<a href="https://github.com/neuland/micro-frontends/tree/master/2-composition-universal" target="_blank">github</a>  
  
トラクターの切り替えセレクターはただのリンクでクリックする度にページがリロードされます。  
右側のコンソールはリクエストがどのように処理されているかを示しています。  
まず赤チームが担当するページ全体のURLが読み込まれ  
その後に青チームと緑チームが作ったコンポーネントが読み込まれています。  
  
javascriptを有効にすると、最初のページ全体のロードだけが行われます。  
トラクターの切り替えは一番最初の例と同じように、クライアントサイドで行われます。  

このサンプルコードはローカルマシンで試せます。  
(docker-composeをインストールする必要があります)  

<pre class="highlight"><code>git clone https://github.com/neuland/micro-frontends.git
cd micro-frontends/2-composition-universal
docker-compose up --build
</code></pre>

DockerはNginxを3000ポートで起動して、さらにそれぞれのチームのサーバーのイメージを起動します。  
`http://127.0.0.1:3000/`にアクセスすると赤のトラクターが表示されます。  
docker-composeのログはネットワークで何が起こっているのかを分かりやすくしてくれます。  
残念ながらログの色の指定はできないので、青チームが緑色で表示されるかもしれませんが。  

ソースコード内の`src` ディレクトリはそれぞれのチームのコンテナにマッピングされています。  
`src`ディレクトリ内のコードに変更があると、そのサーバーは再起動されます。  
自由に中のコードを変えて遊んでみてください。  

#### データ取得とローディング

SSI/ESIの方法の欠点は**一番描画の遅いコンポーネントがページ全体の描画パフォーマンスのボトルネックになる**ことです。  
だから、それぞれのコンポーネントをキャッシュすることがとても大切です。  
描画に時間がかかるし、キャッシュもできないようなコンポーネントは初期描画から外すことも検討しましょう。  
ブラウザから非同期で取得して描画する方が良いかもしれません。  
緑チーム担当の`green-recos` はこの方法を使う候補になります。  

単にSSIを外せば、これを実現できます。    

##### Before

<pre class="highlight"><code>&lt;green-recos sku="t_porsche"&gt;
  &lt;!--#include virtual="/green-recos?sku=t_porsche" --&gt;
&lt;/green-recos&gt;
</code></pre>

##### After

<pre class="highlight"><code>&lt;green-recos sku="t_porsche"&gt;&lt;/green-recos&gt;
</code></pre>

重要事項として、Custome Elementは<a href="https://developers.google.com/web/fundamentals/web-components/customelements#jsapi" target="_blank">self-closing</a>ができません。  
そのため`<green-recos sku="t_porsche" />` とした場合は正しく動作しない可能性があります。  

![data-fetching-reflow](https://micro-frontends-japanese.org/resources/data-fetching-reflow.gif)

このようにしたコンポーネントの描画はブラウザでのみ行われます。  
アニメーションを見てわかる通り、描画する際にはブラウザ内で**スタイルの再計算** が行われます。  
レコメンドの部分は最初はブランクで、APIにリクエストして緑チームのコンポーネントを取得した後で描画されます。  
この時はより多くのスペースが必要になるので、ページのレイアウトを変える必要があります。  

この鬱陶しい再計算を避ける方法はいくつかあります。  
まず、赤チームがレコメンドの枠の高さを固定する方法が考えられます。  
レスポンシブ対応のサイトでは高さを固定する時点でトリッキーですが  
もっと大きな問題点は、このようなチーム間のルールが  
赤チームと緑チームの間に密結合を作ってしまう、ということです。  
もし緑チームがレコメンドのコンポーネントにサブヘッダーを入れた時には  
赤チームは枠の高さを調整しなくてはなりません。  
レイアウトが壊れないように両チームが同時に調整する必要が生まれてしまいます。  

ベターな方法は<a href="https://blog.prototypr.io/luke-wroblewski-introduced-skeleton-screens-in-2013-through-his-work-on-the-polar-app-later-fd1d32a6a8e7" target="_blank">Skelton Screens</a>と呼ばれる方法を使うことです。  
赤チームは`green-recos` コンポーネントのSSIをそのままにしておきます。  
さらに緑チームはサーバーサイドレンダリングの描画メソッドを  
コンポーネントの中身ではなく、その**コンテンツのスケルトン**に置き換えます。  
このスケルトンを使うことででは、本物のコンテンツのレイアウトを使いまわせます。  
この方法なら、**最初にスペースが確保され** 、実際の描画時にガクガク動くことはなくなります。

![skelton](https://micro-frontends-japanese.org/resources/data-fetching-skeleton.gif)

**スケルトンはクライアントサイドでも役立ちます。**  
ユーザーのなんらかのアクションに対してCustom Elementを差し込む場合などは  
とりあえずスケルトンを表示しておいて、データがきたら本当のコンポーネントを表示する  
といった使い方が可能です。  

属性の変更のような簡単な場合でもスケルトンを表示するかどうかを選べます。  
ユーザーはスケルトンで何かが起こっているということは分かるのですが  
APIがすぐにレスポンスを返した場合には、UIがちかちかしてしまいます。  
データを取得しても、しばらくスケルトンを表示しておくなど  
ユーザーのフィードバックをもらいながら賢く実装していきましょう。  

## 著者
**Michael Geers** (@naltatis)  
neuland Büro für Informatikで働くソフトウェアエンジニア。  
E-コマースのよりよいフロントエンドを追及している。  

## 原文
<a href="https://micro-frontends.org">Micro Frontends</a>
なお、原文のページはGithub Pagesでホスティングされています。  
ソースコードは<a href="https://github.com/neuland/micro-frontends/" targe="_blank">こちら</a>で見ることができます。

## 訳者
**小池貴之** (@koiketakayuki)
ふつうのソフトウェアエンジニア

なお、翻訳版のサイトもGithub Pagesでホスティングされています。
ソースコードは<a href="https://github.com/koiketakayuki/micro-frontends/" targe="_blank">こちら</a>で見ることができます。
修正等がありましたら、Pull RequestやIssue、もしくはメールで連絡を頂けると助かります。
