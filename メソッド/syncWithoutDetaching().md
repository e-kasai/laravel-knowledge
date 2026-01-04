
---

#### code例

```php
$item->favorites()->syncWithoutDetaching([auth()->id()]);
```
 ---

#### 例：お気に入り機能
syncWithoutDetaching([auth()->id()]) は
「この商品‐このユーザー」の組み合わせだけを1行足します。
他ユーザーの行は消さないので、ユーザーが増えるほど行も増え、カウントも増えます。

例（favorites 中間テーブル）

 item_id| user_id
 |---|---
  10    |   3     ← Aさんがいいね
  10    |   7     ← Bさんがいいね（追加）
  10    |  12     ← Cさんがいいね（追加）


同じユーザーが同じ商品をもう一度押しても重複は作らない（行は増えない）

---
** いいね数の取り方 **

* 都度数える：$item->favorites()->count()
* 事前集計： $ item = Item::withCount('favorites')->find($id); → $item->favorites_count

---
** 増加しないとき確認する事 **

1. リレーション定義が商品→ユーザーになっている

```php
// Item.php
public function favorites() {
  return $this->belongsToMany(User::class, 'favorites', 'item_id', 'user_id');
}
```

2. 中間テーブルのユニーク制約

```php
unique(['item_id','user_id'])
```
（※user_id単独のuniqueはNG）

3. リロード後にcountしているか
`redirect()->back()` 先で `->count()` する、または `->withCount()` を使う
