
---

#### Mass Assignmentとは
** Item::create([...]) のように 配列を丸ごと渡してレコード作成 ができる仕組み **

---

#### code例
```php
Item::create([
  'item_name'  => '時計',
  'price'      => 5000,
  'seller_id'  => 1
]);
```


---

#### 注意point

* create() は内部で $model->fill([...])->save(); を呼んでいる
* `fill()` を呼ぶということは ** $fillable に書かれているカラムにだけしか入らない **

---

#### リレーションとの違い
* Mass Assignment：配列をモデルに流し込む仕組み（fillable 必須）
* リレーション経由：親モデルからの外部キーは自動で埋まるので $fillable に書かなくてもOK

`code例`

```php
// NG: 普通に create() → seller_id 自分で書かないとエラー
Item::create([
  'item_name' => '時計',
  'price'     => 5000,
  'seller_id' => auth()->id(), // 必要
]);

// OK: 親経由の create() → seller_id 自動で入る
auth()->user()->items()->create([
  'item_name' => '時計',
  'price'     => 5000,
]);
```

---

#### リレーション経由時の注意点

** 親、子モデル両方に外部キーを書いておかないとidが自動で紐づかない **

`例`
```
// User.php
public function items()
{
    return $this->hasMany(Item::class, 'seller_id');
}

// Item.php
public function user()
// テーブル名がitemsだからdefaultのitems_idと違うのでわざわざ書く必要あり
{
    return $this->belongsTo(User::class, 'seller_id');
}
```

両方書いておけば、

* ユーザーから → $user->items
* アイテムから → $item->user
と双方向にアクセスできる。

実務では「片方しか使わないから1つだけ書く」ってことはあんまりなくて、基本は両方に書いておく
** ただ、LaravelはFKを自動で推測してくれるのでuniqueなFK以外は書かなくていい(普通のtable名_id)なら書かない **

---

#### まとめ
* Mass Assignment = 配列からモデルに一気に値を入れる仕組み。
* $fillable で許可するカラムを指定しないとエラー（MassAssignmentException）。
* セキュリティのために「どこまで代入させるか」をきちんと管理するのが大事。
* リレーション経由なら外部キーは自動で補完される。
