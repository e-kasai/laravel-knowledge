
---

概要

request()->is() は 現在のURLパス が特定のパターンと一致するかどうかを判定するメソッド。
ルート名ではなく パス で判定したいときに使う。

使い方
@if (request()->is('login'))
    <p>ログインページです</p>
@endif


URLパスが /login なら true を返す。

ワイルドカード

* を使えば部分一致ができる。

@if (request()->is('items/*'))
    <p>商品関連ページです</p>
@endif


/items/1
/items/edit
/items/anything
など items/ で始まるURL全てにマッチ。

複数条件

カンマ区切りでは書けないので、||（または条件）で繋ぐ。

@if (request()->is('login') || request()->is('register'))
    <p>ログインまたは登録ページです</p>
@endif

具体例
URLパス

/items
/items/10
/items/10/edit

判定例
@if (request()->is('items'))
    <p>一覧ページ</p>
@endif

@if (request()->is('items/*'))
    <p>詳細・編集ページ</p>
@endif

####  routeIs() との違い
機能	request()->is()	request()->routeIs()
判定対象	URLパス	ルート名
ワイルドカード	* あり	* あり
用途	URLベースで判定	名前付きルートベースで判定
注意点

URL構造を変更した場合、判定ロジックも修正が必要になる。

ルート名が安定している場合は routeIs() を使う方が保守性が高い。