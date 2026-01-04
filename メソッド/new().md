
---

#### 役割
new の役割
クラス（設計図）から 実体（オブジェクト＝中身） を作って、その箱に入れる。

`$item = new Item();`
`// $変数名 = new モデル名()`

1. Item というクラスをもとに「新しいオブジェクト」を作る
2. そのオブジェクトを $item という箱に入れる

---

#### 特徴
- 常に新しいインスタンスを作る
-  save()しても既存レコードは消えない＝常に新しいインスタンスだから

---

#### 使用例
```php
//出品処理
    public function storeExhibitItem(ExhibitionRequest $request)
    {
        //transactionで出品データが１部だけ変更されてしまうのを防ぐ(All or nothing)
        // 出品商品新規作成

        DB::transaction(function () use ($request) {
            $validated = $request->validated();
			// Item というクラス（設計図）を元に、空のオブジェクトを1つ作る(空の箱)
            $item = new Item();
            $item->fill([
                'seller_id'     => auth()->id(),
                'item_name'     => $validated['item_name'],
                'brand_name'    => $validated['brand_name'],
                'description'   => $validated['description'],
                'price'         => $validated['price'],
                'condition'     => $validated['condition'],
            ]);

            //商品画像アップロード
            if ($request->hasFile('image_path') && $request->file('image_path')->isValid()) {
                // 画像を保存
                $item->image_path = $request->file('image_path')->store('items', 'public');
            }
            $item->save();
```