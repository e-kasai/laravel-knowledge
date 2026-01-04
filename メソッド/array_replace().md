
---

#### 役割

* 配列を上書き合成する (変更した差分だけ上書きする)
* キーに一致する値を置き換える

#### 例
```php
// $base をベースにして、$updatedAddress に同じキーがあればその値で上書きする。

$shipping = array_replace($base, $updatedAddress);
```

#### 実行イメージ

Before
```php
$base = [
    'postal_code' => '111-1111',
    'address'     => '東京都港区1-1-1',
    'building'    => 'スカイタワー101',
];

$updatedAddress = [
    'postal_code' => '222-2222',
    'building'    => null,
];
```

After
```php
$shipping = [
    'postal_code' => '222-2222', // 上書き
    'address'     => '東京都港区1-1-1', // baseそのまま
    'building'    => null, // draftで上書き
];
```

** つまり変更した差分だけ上書き **

---
