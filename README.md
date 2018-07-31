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
データベースからUIに至るまでをすべて実装します

##### モノリシックなフロントエンド

![Frontend Monolith](https://micro-frontends.org/ressources/diagrams/organisational/monolith-frontback-microservices.png)

##### マイクロフロントエンド

![Frontend Monolith](https://micro-frontends.org/ressources/diagrams/organisational/verticals-headline.png)


## モダンなWebアプリとは
前文で「モダンなウェブアプリ」という言葉を使ったが  
それに関わる用語を定義しておこう。

視野を広げるため、[Alan Balkan](https://ar.al/)の書いた[「Documents‐to‐Applications Continuum」](https://2018.ar.al/notes/the-documents-to-applications-continuum/)を紹介しよう。

彼が思いついたこのWebサイトの分類は  
左側に行くほどリンクや文字のような静的なものを  
右側に行くほど写真の編集ツールのような動的なものを  
配置するというものだった。 
  
もしあなたのUIがこの指標で左側寄りなら  
そのプロジェクトはサーバーサイドに完結しているのが良いだろう。
このモデルでは、ページはすべてのコンポーネントのHTML文字列を集めて組み合わせるだけである。
コンテンツの更新はページの再読み込みか、ajaxで行われる。  
[Gustaf Nilsson Kotte](https://twitter.com/gustaf_nk/)がこれについて[まとめた記事](https://gustafnk.github.io/microservice-websites/)を書いている  
  
もしあなたのUIがユーザーの動作に対してすぐにフィードバックをしてあげたいなら  
前述のサーバーで完結しているモデルは、不十分だ。  
[Optimic UI](https://www.smashingmagazine.com/2016/11/true-lies-of-optimistic-user-interfaces/)や
[Skelton Screens](https://micro-frontends.org/#the-dom-is-the-api)のようなテクニックを使いたいなら  
UIを**デバイス上で更新する必要がある**。  
また、Googleの
[Progressive Web Apps](https://developers.google.com/web/progressive-web-apps/)
では高速なパフォーマンスと、すべての人にコンテンツを届けることの両立(Progerssive enhancement)を説明している。  
このようなアプリケーションは先ほどの指標の真ん中あたりに位置している。  
これもまたサーバー完結モデルでは不十分で、アプリケーションを**ブラウザと統合**することが必要になる。  
これがこの記事のテーマである。

## マイクロフロントエンドの大事な考え

+ テクノロジーとの分離

各チームは実装するのに選ぶ技術を、他のチームとの調整をせずに選ぶべきである。
CustomElementはその技術の実装を隠して、技術に依存しないインターフェースを提供してくれる。  

+ チームをまたいでコードを共有しない

例え使っている技術が同じでもコードを共有しないこと。  
それ自身で完結しているアプリケーションを作ること。  
共有の状態や、グローバルの変数に依存しない。

+ チームのprefixを決める

チームが上手く分離されていない場合、変数やファイルのprefixを決めておく。
これらが使われたCSS、Event, LocalStorageやCookieは  
誰が受け持つべきかをはっきりさせたり、衝突を避けたりするのに役立つ。

＋ 自作APIではなく、ブラウザネイティブのAPIを使う

コンポーネント間のデータのやりとりには  
グローバルなPub/Subシステムではなく  
ブラウザネイティブのイベントを用いること。  
もし自作のAPIが必要なら、できるだけシンプルにする。  

+ Resilientなサイトを作る
例えjavascriptが動かなくかったり、途中で止まったりしても、そのサイトは役立つかもしれない。  
体感パフォーマンスを上げるためにUniversal RenderingやProgressive Enhancementの考え方を取り入れよう
