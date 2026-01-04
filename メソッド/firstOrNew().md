
`firstOrNew()` は **Eloquentの便利メソッド** で、  
「条件に一致するレコードがあればそれを取得、なければ**新しいモデルインスタンスを作るだけ**（DBにはまだ保存しない）」という動きをします。

---

### 基本形

`$model = Model名::firstOrNew(['条件カラム' => 値]);`

---

### 具体例

#### 1. 既存レコードがある場合

`$profile = Profile::firstOrNew(['user_id' => 1]);`

- `profiles` テーブルに `user_id = 1` のデータがあれば **それを取得**。
- `$profile->exists` は `true`。
    

---

#### 2. レコードがない場合

`$profile = Profile::firstOrNew(['user_id' => 99]);`

- `profiles` テーブルに `user_id = 99` がなければ  
    **新しい Profile インスタンスを返す**（この時点ではDBに保存されていない）。
    
- `$profile->exists` は `false`。
    

---

### 保存するには

レコードが新規作成された場合は `save()` が必要です。
fill()やsave()すると上書き更新される = 古い情報を更新するような用途に向いている

`$profile = Profile::firstOrNew(['user_id' => auth()->id()]); $profile->avatar_path = $avatarPath;` 
`$profile->save();`

---

### `firstOrNew()` と `firstOrCreate()` の違い

|メソッド|DBに即保存？|使いどころ|
|---|---|---|
|**firstOrNew()**|しない=save()が必要|値を上書きしてから保存したいとき|
|**firstOrCreate()**|する=save不要|すぐに保存したいとき|

---

### 既存レコードを上書きしたくないときは？
[[new()]]　= 常に新しいインスタンス作成する

### まとめ

`firstOrNew()` は「**既存なら取得、新規なら空のインスタンスを返す**」だけの処理です。  
新規インスタンスはDBに反映されていないので、必ず `save()` を呼んで保存する必要があります。