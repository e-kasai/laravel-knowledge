
---

#### 役割

```blade
//route('ルート名', [パラメータ])
```
* 第一引数 → routes/web.php に定義したルート名 
* 第二引数 → そのルートで URIに埋め込むべきパラメータ

---

#### 具体例
```php
//こういうルートがあるとする
Route::get('/purchase/{item_id}', [PurchaseController::class, 'show'])
    ->name('purchase.show');
```
↓
```blade
//route('ルート名', [パラメータ])
// ルート名 = purchase.show パラメータ = item_id
route('purchase.show', ['item_id' => $item->id])
```
パラメータ込みのURLを生成してくれる

---

#### 使い方

Bladeでの使い方
リンクを作るときによく出てくる：

```html
<a href="{{ route('purchase.show', ['item_id' => $item->id]) }}">
    購入手続きへ
</a>
```


コントローラでの使い方
リダイレクトするときも使える：
```php
return redirect()->route('purchase.show', ['item_id' => $item->id]);
```

これで purchase.show のルートへリダイレクトできる。
もしURL構造が変わっても ルート名が変わらない限り壊れないから安心。

つまり「ルート名をキーにしたURLジェネレーター」ってイメージ

---

#### まとめ

* ルートに パラメータ（例: {item_id}) がある→ 必ず値を渡す必要がある
* その値を route() の第2引数で指定する
* これがないと「URLを組み立てられない」とLaravelが怒る



