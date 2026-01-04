
---

#### 原則
* HTMLフォームは GET か POST しか書けない
* だから PATCH や PUT、DELETE を使いたいときは Laravel流の「メソッド偽装」を使う
	`<form method="POST">` にして `@method('PATCH')`
* routes/web.php 側は Route::patch(...)とかdeleteとかOK

---

#### メソッド偽装 (blade)

```html
<form method="POST" action="{{ route('address.update', $item) }}">
    @csrf
    @method('PATCH')
    <!-- フィールド群 -->
    <button type="submit">更新</button>
</form>
```

#### web.php
```php
// routes/web.php
Route::patch('purchase/address/{item}', [PurchaseController::class, 'updateShippingAddress'])
    ->name('address.update');
```


#### まとめ
* 「フォームはPOST実体＋@method」「ルートはPATCH」って合わせるのが、REST的にもLaravel的にも王道