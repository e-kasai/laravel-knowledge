---
tags: withPivot
---


## 中間テーブルにPK,FK以外のカラムがある場合　= withPivot()

中間テーブルに `created_at`, `updated_at`, `note` などの**デフォルトカラム以外**がある場合に使用。

**デフォルトカラム**
- 中間テーブルのPK
- つなぐ両テーブルのFK

---
#### 何のために必要？
 中間テーブルのカラムを引っ張ってきたいときに必要

---

#### 例:ユーザーがいいねした日時を取得したい

```php
$user = User::find(1);

// このユーザーがお気に入り登録したアイテムを取得
foreach ($user->favoriteItems as $item) {
    echo $item->item_name;         // itemsテーブルのカラム
    echo $item->pivot->created_at; // favoritesテーブル（中間テーブル）のcreated_at
}
```

### favoritesテーブルに日時がある

|カラム名|説明|
|---|---|
|id|PK|
|user_id|お気に入りしたユーザー|
|item_id|お気に入り対象のアイテム|
|created_at|登録日時|
|updated_at|更新日時|

---

#### withPivotを書く場所 = 中間テーブルでつながっている両モデル

#### Userモデル
```php
public function favoriteItems() {     
	return $this->belongsToMany(Item::class, 'favorites', 'user_id', 'item_id')                 
	->withPivot('created_at', 'updated_at'); //これが必要
}
```

#### Itemモデル

```php
public function favoritedByUsers() {     
	return $this->belongsToMany(User::class, 'favorites', 'item_id', 'user_id')                 
	->withPivot('created_at', 'updated_at'); //これが必要
}
```

---

#### なぜpivotが必要？

この例の場合「このユーザーがいつそのアイテムをいいねしたか（日時）」は 中間テーブル（favorites)にしか存在しないから。
そこでpivotを使うとEloquentが、中間テーブル favorites の行を「pivotオブジェクト」として $item->pivot にくっつけてくれる。

pivot のイメージ
```php
$item->pivot // => Illuminate\Database\Eloquent\Relations\Pivot インスタンス
[
    "user_id"   => 1,
    "item_id"   => 5,
    "created_at"=> "2025-09-01 10:00:00",
    "updated_at"=> "2025-09-01 10:00:00"
]
```
---

#### まとめ

belongsToMany専用の仕組み
中間テーブルのカラムは 直接モデルには入らない → 代わりに $model->pivot で参照
呼び出し方 = withPivot('必要なカラム名') をつける


#### 切り分け

✅ デフォルトで pivot に入るカラム
中間テーブルの 外部キー（user_id, item_id）　と　id（もしカラムを作っていれば）
これらは withPivot を書かなくても 自動で $model->pivot に入っている。


❌ 自動で入らないカラム
created_at, updated_at　や　その他のカスタムカラム（reason, body, status など）
これらは 明示的に withPivot(...) が必要。

🔑 つまり：
「外部キー（接続に必須なもの）」はデフォで pivot に含まれる。
「それ以外の情報（タイムスタンプやカスタム列）」は必要な分だけ withPivot で宣言する