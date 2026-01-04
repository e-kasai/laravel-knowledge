
---
#### 役割
* put($key, $value) は「このキーで値を保存して！」という意味。

例：
```php
//sessionにnameというキーでEKを保存
//session('name')で値を呼び出せる
session()->put('name', 'EK');
```

---
#### 実際の例

```php
// これは「updated_address」という大きな配列の中に、さらに item_id ごとに住所を保存する」っていうイメージ
session()->put("updated_address.{$item->id}", [
    'postal_code' => $v['postal_code'],
    'address'     => $v['address'],
    'building'    => $v['building'] ?? null,
]);
```

---
#### コード解説

* 例えば、`$item->id = 13` の場合は`updated_address.13` というキーに保存される
* 保存される中身は配列：
```php
[
    'postal_code' => '123-4567',
    'address'     => '東京都新宿区〇〇',
    'building'    => 'マンション101'
]
```

** 具体的な保存イメージ **

もし2つの商品に配送先を設定したら、セッションはこんな感じ

```php
[
    "upadted_address" => [
        13 => [
            "postal_code" => "123-4567",
            "address"     => "東京都新宿区〇〇",
            "building"    => "マンション101",
        ],
        27 => [
            "postal_code" => "987-6543",
            "address"     => "大阪市北区△△",
            "building"    => null,
        ],
    ]
]
```

→ つまり「商品ごとに配送先を覚えておける」

#### 取り出しの例
```php
$updatedAddress = session("updated_address.{$item->id}", []);
```

* "draft_address.{$item->id}" はキー名で、たとえば $item->id が 5 なら "draft_address.5"
* session("キー名", デフォルト値) は、キーが存在しないときに 第二引数の値を返す
* ここでは []（空の配列）を指定してるから、セッションに保存されてなければ「新しい空配列を返す」



---
#### session()->put()のメリット

* DBにまだ書き込まない段階で情報を保持できる
   ex: 購入ボタンを押すまではセッションでキープ → 購入確定時に 正式にtransactions テーブルへ保存など
* 商品ごとに別の配送先を使える
   ex: 本は自宅に、プレゼントは実家になど