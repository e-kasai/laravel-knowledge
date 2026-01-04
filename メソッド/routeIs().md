
---

aravel routeIs() メモ
概要

routeIs() は 現在のリクエストのルート名が特定の名前と一致するかどうかを判定 
routeごとに表示を変えたいときなどに使う

使い方
@if (request()->routeIs('login'))
    <p>ログインページです</p>
@endif


ルート名が login なら true を返す。

複数条件
@if (request()->routeIs('login', 'register'))
    <p>ログインまたは登録ページです</p>
@endif

ワイルドカード

* を使って部分一致が可能。

@if (request()->routeIs('items.*'))
    <p>商品関連ページです</p>
@endif


items.index

items.show

items.edit
など items. で始まるルート全てにマッチ。



具体例
ルート定義
Route::get('/items', [ItemController::class, 'index'])->name('items.index');
Route::get('/items/{id}', [ItemController::class, 'show'])->name('items.show');

Blade での判定
@if (request()->routeIs('items.index'))
    <p>商品一覧ページ</p>
@endif


/items にアクセスしたときだけ表示される。

よくある勘違い

url() や path() の比較ではない。
request()->is() は パス を判定するが、routeIs() は ルート名 を判定する。