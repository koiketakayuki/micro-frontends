モダンなウェブアプリを、異なるjsフレームワークを  
使う複数のチームで作るためのテクニック

## マイクロフロントエンドとは？

マイクロフロントエンドという言葉は  
2016年の終わりに[ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar/techniques/micro-frontends) 
で言及されました。  
それはマイクロサービスの考え方をフロントエンドに拡張したものです。  
現在のWebのトレンドは、多機能でパワフルなSPAです。  
SPAはフロントエンドとバックエンドを切り離すという、マイクロサービスの考え方に基づいています。

開発をすすめていくと、特に複数のチームに管理された場合にありがちですが  
フロントエンド層が肥大化して管理が難しくなりがちです。  
これを「モノリシックなフロントエンド」と呼びます。  
  
マイクロフロントエンドの根底にある考えは   
ページを独立したチームによって管理された機能の集合体と捉えることです。  
それぞれのチームは特定の領域やミッションに特化しています。  
チームは機能横断的な作りになっていて、その機能をend-to-end、つまり  
データベースからUIに至るまでをすべて実装します。

##### モノリシックなフロントエンド

![Frontend Monolith](https://micro-frontends.org/ressources/diagrams/organisational/monolith-frontback-microservices.png)

##### マイクロフロントエンド

![Frontend Monolith](https://micro-frontends.org/ressources/diagrams/organisational/verticals-headline.png)


## モダンなWebアプリとは
前文で「モダンなウェブアプリ」という言葉を使いましたが、
それに関わる用語を定義しましょう。

視野を広げるため、[Alan Balkan](https://ar.al/)の書いた[「Documents‐to‐Applications Continuum」](https://2018.ar.al/notes/the-documents-to-applications-continuum/)を紹介します。

彼が考えたこのWebサイトの指標は  
左側に行くほどリンクや文字のような静的なものを  
右側に行くほど写真の編集ツールのような動的なものを  
配置するというものでした。 
  
もしあなたのUIがこの指標で左側寄りなら  
そのプロジェクトはサーバーサイドに完結しているのが良いでしょう。
このモデルでは、ページはすべてのコンポーネントのHTML文字列を集めて組み合わせるだけです。
コンテンツの更新はページの再読み込みか、ajaxで行われます。  
[Gustaf Nilsson Kotte](https://twitter.com/gustaf_nk/)がこれについて[まとめた記事](https://gustafnk.github.io/microservice-websites/)を書いています。  
  
もしあなたのUIがユーザーの動作に対してすぐにフィードバックをしてあげたいなら  
前述のサーバーで完結しているモデルは、不十分です。  
[Optimic UI](https://www.smashingmagazine.com/2016/11/true-lies-of-optimistic-user-interfaces/)や
[Skelton Screens](https://micro-frontends.org/#the-dom-is-the-api)のようなテクニックを使いたいなら  
UIを**デバイス上で更新する必要があります**。  

また、Googleの
[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)
では高速なパフォーマンスと、すべての人にコンテンツを届けることの両立(Progerssive Enhancement)を説明しています。  
このようなアプリケーションは先ほどの指標の真ん中あたりに位置しています。  
これもまたサーバー完結モデルでは不十分で、アプリケーションを**ブラウザと統合**することが必要になります。  
これが今記事のテーマです。

## マイクロフロントエンドの根底にある考え

+ テクノロジーとの分離

各チームは実装するのに選ぶ技術を、他のチームとの調整をせずに選ぶべきです。
CustomElementはその技術の実装を隠して、技術に依存しないインターフェースを提供してくれます。  

+ チームをまたいでコードを共有しない

例え使っている技術が同じでもコードを共有してはいけません。  
それ自身で完結しているアプリケーションを作りましょう。  
共有の状態や、グローバルの変数に依存してはいけません。

+ チームのprefixを決める

チームが上手く分離されていない場合、変数やファイルのprefixを決めておきましょう。
これらが使われたCSS、Event, LocalStorageやCookieは  
誰が受け持つべきかをはっきりさせたり、衝突を避けたりするのに役立ちます。

+ 自作APIではなく、ブラウザネイティブのAPIを使う

コンポーネント間のデータのやりとりには  
グローバルなPub/Subシステムではなく  
ブラウザネイティブのイベントを用いるましょう。  
もし自作のAPIが必要なら、できるだけシンプルにしましょう。  

+ 不測の事態に強い(Risilient)サイトにする

例えjavascriptが動かなくかったり、途中で止まったりしても、そのサイトは役立つかもしれません。  
体感パフォーマンスを上げるためにUniversal RenderingやProgressive Enhancementの考え方を取り入れましょう

---

## DOMがAPIになる

Custom Elementsはブラウザにアプリを統合するのに役立ちます。  
それぞれのチームは選択したフレームワークを用いて、コンポーネントを実装して  
それをCustom Elementの中に隠蔽します。(例: `<order-minicart></order-minicart>`)  
このタグの名前が他のチームがコンポーネントを使う際のAPIとしての役割を果たします。  
こうすることで、このコンポーネントがどんなライブラリを使って作られたのかを知る必要なしに使えます。  
ただDOMとやりとりすればいいのです。

ただ、Custom Elementだけではすべての問題を解決できません。  
Progressive EnhancementやUniversal Rendering、ルーティングなどを実現するためには  
別のテクノロジーが必要になります。  

以下のページは二つのセクションに分かれています。
最初にページの組み立て(どのような別々のチームに実装されたコンポーネントを組み合わせるか)について、  
次にクライアントサイドでのページ遷移の例を説明します。

## ページの組み立て(Page Composition)

色んなフレームワークで書かれたコードを、クライアントとサーバー両方に統合する  
ということ以外にも、jsの分離、CSSの衝突の避け方、遅延読み込み  
チーム間のリソース共有、データ取得、ローディングなど議論すべきことたくさんがあります。  
これらのトピックは一度にまとめて紹介します。

### プロトタイプ

トラクターの販売店の販売ぺージが以降の説明のモデルになります。  

このページには３つのトラクターを切り替える**セレクター**がついています。  
切り替わるとトラクターの画像、名前、値段、リコメンドが更新されます。  
また、クリックすると選択されているトラクターをバスケットにいれる**購入ボタン**があります。  
さらに上部にはバスケットの状態に応じて表示が切り替わる**ミニバスケット**がついています。  

![Base Protptype](https://micro-frontends.org//ressources/video/model-store-0.gif)

[ブラウザで試す](https://micro-frontends.org/0-model-store/)&[Github](https://github.com/neuland/micro-frontends/tree/master/0-model-store)

すべてのHTMLはクライアントサイドでjsとes6のテンプレートストリングを使って生成されています。  
他になんの依存もありません。  
単にマークアップと状態を分離して、何か変更があった際にはすべてのHTMLを再描画してるだけです。  
DOMの変更分だけを再描画したり、Universal Renderingといったものは今は使われていません。  
また、タスクをチームごとに分割といったこともしていません。ひとつのjs/cssにすべてが書かれています。


### クライアントサイドとの統合
以下の例では、ページを３つのコンポーネントに分離して、それぞれの実装をチームごとにアサインします。  
**Checkoutチーム(青)** は購入のすべてのプロセスを担当します。ここでは購入ボタンとミニバスケットです。  
**Inspireチーム(緑)** はこのページのレコメンドを担当します。  
**Productチーム(赤)** ページ全体を担当します。どの機能が必要で、どこに置くかを決めます。  

![Client-side integration](https://micro-frontends.org/ressources/screen/three-teams.png)

[ブラウザで試す](https://micro-frontends.org/1-composition-client-only/)&[Github](https://github.com/neuland/micro-frontends/tree/master/1-composition-client-only)


ページはProductチームが決めた商品名や画像などを表示しますが、それだけでなく  
他のチームが作ったコンポーネント(Custom Elements)もページに含まれます。

#### Custom Elementの作り方

例として購入ボタンを考えてみましょう。  
Productチームは`<blue-buy sku="t_porsche"></blue-buy>`とHTMLの表示したい位置に書けばページにボタンを追加できます。  
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

![Custom-Element](https://micro-frontends.org/ressources/video/custom-element.gif)

コンポーネントの名前に関するルールが一つだけあります。  
HTMLの互換性を保つためにコンポーネント名に`-`を含まなければなりません。  
以降の例では`[チームカラー]-[機能名]`という命名規則が用いられます。  
これはチーム間でのコンポーネントの名前衝突を避け、責任を明確にするという目的があります。  

#### 親->子間でのデータの受け渡し/DOMの更新

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

![Custom-Element rerender](https://micro-frontends.org/ressources/video/custom-element-attribute.gif)

このプロパティが書き換わった際に再描画される機能を自前で実装する時は  
その挙動を`attributeChangedCallback`に、ウォッチするプロパティを  
`observedAttributes`に定義しておきます。

<div class="highlight"><pre class="highlight"><code>const prices = {
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
</code></pre></div>

コードの重複を避けるために、`render()`メソッドが定義されてます  
(`constructor`と`attributeChangedCallback`から呼び出されます)。  
このメソッドは再描画に必要な情報を集めてきます。  
Custom Element内でテンプレートエンジンやライブラリを使う場合に  
初期化コードを書くのもこの`render()`内部です。

### ブラウザサポート

上の例で使われているCustom ElementはCustom Element V1 Specに基づいています。  
これはChromeとSafariとOperaでサポートされています。  
ただし`document-register-element` に関して言えば主要ブラウザで動かすためにpolyfillが必要です。  
Custom Element内部では[ほとんどのブラウザでサポートされている](https://caniuse.com/#feat=mutationobserver)Mutation Observer APIを使っているため
裏側でトリッキーなDOMの変更検知をしている、といったことはありません。

### フレームワーク対応
Custom ElementはWeb標準なのでAngular, React, Preact, Vue, Hyperappなど  
主要なjsフレームワークはこれをサポートしています。  
しかし細かい点を見ると、対応できていなかったり、バグがあったりします。  
[Custom Elements Everywhere Rob Dodson](https://custom-elements-everywhere.com/)では  
これらの問題をまとめてくれています

### 子 -> 親間(もしくは兄弟間)のデータのやりとり/DOMイベント

親から子にデータを渡すだけでは不十分です。  
先ほどの例では、購入ボタンを押したときにミニバスケットの表示を  
変更する必要があり、これは親から子へのデータの受け渡しだけでは実現できません。  

この２つのコンポーネントはCheckoutチーム(青)の担当です。  
購入ボタンとミニバスケットを繫ぐAPIを実装することも可能ですが  
その方法では、購入ボタンとミニバスケットが密結合し、コンポーネントの分離原則が破られてしまいます。  

クリーンな方法は、PubSubパターンを使うことです。  
コンポーネントはメッセージを発行し、他のコンポーネントは  
そのメッセージを(どのコンポーネントから来たかは知らずに)受け取ります。  
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

この方法ではミニバスケットコンポーネントは外部の`window`にリスナーを追加します。  
多くのアプリケーションでは大丈夫なはずですが  
もしあなたがこの方法を気持ち悪いと思うのなら  
ページがコンポーネントの変更を検知して  
ミニバスケットの`refresh()`メソッドを呼び出すことで
再描画する、という書き方も可能です。  

<pre class="highlight"><code>// page.js
const $ = document.getElementsByTagName;

$('blue-buy')[0].addEventListener('blue:basket:changed', function() {
  $('blue-basket')[0].refresh();
});
</code></pre>

DOMのメソッドを呼び出すということは普通はやりませんが
[video-elemt api](https://developer.mozilla.org/de/docs/Web/HTML/Using_HTML5_audio_and_video#Controlling_media_playback)ではよく使われます。  

可能なら宣言的な方法(属性が変わった際に自動で更新される方法)の方が望ましいでしょう。

## サーバーサイドレンダリング/ユニバーサルレンダリング

Custom Elementはコンポーネントとブラウザを統合するすばらしい技術ですが
初期描画が遅いという欠点があります。  
ユーザーはすべてのjavascriptがロードして実行されるまで  
白い画面を見続けることになるでしょう。  
javascriptがロードや実行に失敗したら何が起こるかを  
考えるのは良いことです。

[Jeremy Keith](https://adactio.com/)が[Resilient Web Design](https://resilientwebdesign.com/)でこの問題についてまとめています。

よって大事なコンテンツをサーバーサイドでレンダリングすることが大切になるのですが  
悲しいことにweb componentの仕様では、サーバーサイドレンダリングに関する言及はありません。

##  Custom Element + サーバーサイドレンダリング = ❤️

前の例でサーバーサイドレンダリングを有効にするためにはリファクタが必要です。  
各チームはexpressサーバーからコンポーネントを配信します。  
こうすることで、URL経由でコンポーネントの`render()` メソッドを呼び出せます。

<div class="highlight"><pre class="highlight"><code>$ curl http://127.0.0.1:3000/blue-buy?sku=t_porsche
&lt;button type="button"&gt;buy for 66,00 €&lt;/button&gt;
</code></pre></div>

Custom Elementのコンポーネントの名前はURLのパスとして使用されています。  
属性はGETパラメータとして渡されます。  
こうすることですべてのコンポーネントをサーバーサイドレンダリングすることが可能です。  　

以下の`<blue-buy>` の例は**Universal Web Component**とほとんど同じことができます。  

<pre class="highlight"><code>&lt;blue-buy sku="t_porsche"&gt;
  &lt;!--#include virtual="/blue-buy?sku=t_porsche" --&gt;
&lt;/blue-buy&gt;
</code></pre>



