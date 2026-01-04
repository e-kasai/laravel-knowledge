
---

`collect();` は **空のコレクション（Collection）を作るコード** です。

---

## **ポイント**

- `collect()` は **Laravel のヘルパー関数**。
    
- 中身が空っぽの `Illuminate\Support\Collection` オブジェクトを返します。
    

---

## **なぜ使うのか**

例えば、ビューに `$myList` という変数を渡すけど **「中身がまだないケース」** がある場合、  
普通に `null` や空配列 `[]` を渡すよりも安全です。

### **例**

```php
// コントローラー 
return view('items.index', [     
	'items'  => $items,   // おすすめリスト     
	'myList' => collect(), // マイリストは空 ]
);
```

Blade 側：

```html
@foreach ($myList as $item)     
<div>{{ $item->name }}</div> 
@endforeach
```

`collect()` なら `$myList` が空でも **foreach がエラーにならず安全** に動きます。

---

## **比較**

|書き方|中身|foreach時の動作|
|---|---|---|
|`$myList = collect();`|空のコレクション|OK（0回ループ）|
|`$myList = [];`|空配列|OK（0回ループ）|
|`$myList = null;`|null|**エラー** (`Invalid argument supplied for foreach()`)|

---

## **応用**

後でデータを追加するときも柔軟です。

```php
$myList = collect(); 
$myList->push('item1'); 
$myList->push('item2');  
// ['item1', 'item2']
```

---

### **まとめ**

- `collect()` は **安全に使える空の入れ物**。
    
- `foreach` や `map`, `filter` など **Collectionの便利メソッド**が使える。
    
- Bladeでのエラー防止＋可読性アップのため、初期化時には `collect()` が推奨されます。