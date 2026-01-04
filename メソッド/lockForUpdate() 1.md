
---

#### 役割

lockForUpdate() は データベースの行を「処理中ロック」する魔法。
「この行は今私が触ってるから他の人は待って！」とDBに宣言する。

---

#### 使う場面

使う場面（例え話）

* フリマアプリの購入処理
同じ商品をAさんとBさんが同時に購入ボタンを押したら…

ロックなし → 両方「在庫あり」と見えて二重購入のバグ。
ロックあり → 先に取った方だけが購入成功、後の人は待ち・失敗。

* 銀行口座の引き落とし
同じタイミングで2人が「残高1万円の口座から1万円引き落とし」を依頼したら…

ロックなし → 残高マイナスになる可能性。
ロックあり → どちらか一方だけが成功、整合性が保たれる。

---

#### code
```php
DB::transaction(function () use ($item) {
	//このitem１行分のデーターをlockする
    $locked = Item::whereKey($item->id)->lockForUpdate()->first();
	
	//現時点でのDBの状態（is_soldカラム）をチェックしている
    if ($locked->is_sold) {
        throw new \RuntimeException('すでに売り切れです。');
    }
	// その商品($locked)の is_sold カラムを true（売却済み）にして、DBに保存する
    $locked->update(['is_sold' => true]);
});
```

* `whereKey()` は 主キー（通常はidカラム）で絞り込むためのEloquentメソッド。
    SQLでいうと `WHERE id = ?` に変換される。

#### $lockedはどこから来てる？

`$locked = Item::whereKey($item->id)->lockForUpdate()->first();` の結果 
* つまり　`$lockedはobject`

* 中身のイメージ
```php
$locked->id        // 商品ID
$locked->item_name // 商品名
$locked->price     // 価格
$locked->is_sold   // 売却フラグ
```

---

#### point

* 必ずトランザクションの中で使う
トランザクションがないとロックが効かない。

* 行レベルロック
商品テーブルのその1行だけをロックする。他の商品は他ユーザーが触れる。

* 同時処理を直列化
並列アクセスを「順番待ち」に変える。

#### 実際のコード例
```php
DB::transaction(
                function () use ($item, $user, $profile, $request) {
                    // 同時購入を防止
                    $locked = Item::whereKey($item->id)->lockForUpdate()->first();

                    if ($locked->is_sold) {
                        // 直前に売れた場合の二重購入防止
                        throw new \RuntimeException('この商品はすでに売り切れです。');
                    }

            Transaction::create([
                'item_id'               => $locked->id,
                'buyer_id'              => $user->id,
                'purchase_price'        => $locked->price,
                'payment_method'        => (int) $request->input('payment_method'),
                'shipping_postal_code'  => $profile->postal_code,
                'shipping_address'      => $profile->address,
                'shipping_building'     => $profile->building,
            ]);
                $locked->update(['is_sold' => true]);
        });
        return redirect()->route('items.index')
            ->with('message', '購入が完了しました。');
```
