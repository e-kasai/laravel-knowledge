
---

データベース上のレコードを直接更新する
配列で一括更新したいときに使う安全なメソッド

```php
$attendance->update([
    'clock_in' => '09:00',
    'clock_out' => '18:00',
]);
```

###  update() と save() の違い

| 比較項目 | update() | save() |
|-----------|-----------|--------|
| 使い方 | 連想配列で指定 | モデルのプロパティに代入して呼ぶ |
| 動作 | 一括でDB更新 (UPDATE) | モデルの変更内容を検知してDB更新 |
| $fillable制約 | 適用される | 適用されない（モデルの全属性OK） |
| 戻り値 | 成功した件数 or true | 成功したら true |

書き方の違い例
```php
// update() パターン
$attendance->update(['clock_in' => '09:00']);

// save() パターン
$attendance->clock_in = '09:00';
$attendance->save();
```

どちらも最終的にデータは更新されますが、

`update()` は 安全性が高い（fillableで制限）
`save()` は 柔軟性が高い（fillable関係なし）

---

### 注意点
1. updateは配列をとるので条件分岐は中で使えない

```php
//これはNG
$attendance->update([ 
	if ($update->new_clock_in) { $attendance->clock_in = $update->new_clock_in; } 
	if ($update->new_clock_out) { $attendance->clock_out = $update->new_clock_out; } 
]);
// こう書く↓条件分岐が必要な場合は一度配列を組み立ててから渡す
$updateData = [];

if ($update->new_clock_in) {
    $updateData['clock_in'] = $update->new_clock_in;  //あとでまとめて更新するために、配列に詰めてる
}
if ($update->new_clock_out) {
    $updateData['clock_out'] = $update->new_clock_out;
}

if (!empty($updateData)) {                   //何も変更項目がない場合は update しない
    $attendance->update($updateData);        //update() で配列に入っている項目だけをまとめて更新
}
```
