

### `create()`はEloquentのレコード作成メソッド

### サンプルコード


```php
// $user = auth()->user();   ログイン中ユーザー(例: ID=5)
// $items = $user->items;    items() リレーションを呼び出して商品一覧を取得
auth()->user()->items()->create([     
	'item_name'   => 'テスト商品',     
	'price'       => 1000,     
	'color'       => 'red',     
	'condition'   => 1,     
	'description' => 'テスト説明',     
	'image_path'  => 'storage/test.jpg',     
	'is_sold'     => false, 
]);
```

---

### 処理の流れ

1. **`auth()->user()`**  
    ログイン中のユーザーのモデルインスタンス(1レコードをobject化したもの)を取得
    
	実際のイメージはこんな感じ↓
```php
App\Models\User {#1234
  id: 5,
  name: "EK",
  email: "ek@example.com",
  created_at: "2025-08-31 12:34:56",
  updated_at: "2025-08-31 12:34:56",
```

---

2. **`items()`**  
    User モデルで定義したリレーションを呼び出し、Item モデルへのリレーションオブジェクトを返す
    
    ```php
    public function items() {     
	    return $this->hasMany(Item::class, 'seller_id'); 
	}
    ```
    
        
	SQLだとこう　(seller_idが5のitemsを取得してる)
	```sql
	SELECT * FROM items WHERE seller_id = 5;
	```

	**ここでのポイント：`items` は単なる配列じゃなく、`Eloquent クエリビルダー`なのでメソッドチェーンで柔軟に条件を追加できる**

	ex:
	```php
	// まだ売れていない商品のみ取得
	$unsoldItems = auth()->user()
    ->items()
    ->where('is_sold', false)
    ->get();
	```
---

3. **`->create([...])`**  
    渡された配列を元に **Item レコードを新規作成**。  
    リレーション経由なので **`seller_id` は自動補完** される。

---

4. **INSERT 発行**  
    データベースの `items` テーブルに INSERT 文を発行。
    


---

### 戻り値の利用例

```php
$item = auth()->user()->items()->create([     
	'item_name'   => 'Laravel本',     
	'price'       => 2000,     
	'color'       => 'blue',     
	'condition'   => 1,     
	'description' => '状態良好',     
	'image_path'  => 'storage/items/book.jpg',     
	'is_sold'     => false, 
	]);  
	dd($item->id); // 新規登録されたレコードのIDを確認
```

---

### まとめ

- リレーション経由なら外部キー（`seller_id`）は **自動設定されるのでfillableにいれなくてOK**。
    
- `create()` は `$fillable` に記載されているカラムのみ登録される
    
- 戻り値は**作成した `Item` モデルのインスタンス**なので、そのまま ID 取得や後続処理に使える。

---

### リレーション使わない場合

 **`create()` をリレーション経由でなく直接 `Item::create([...])` と書く場合**
 - `fillable` に `seller_id` を入れるか
 - 手動で `seller_id` をセットする必要があります。

```php
// リレーションを使わない場合の例
Item::create([
    'seller_id' => auth()->id(),
    'item_name' => '直接登録',
    ...
]);
```


