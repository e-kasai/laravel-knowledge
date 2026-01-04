
### 仕組み

たとえばプロフィール編集フォームにこう書いてあるとします：

`<input type="file" name="avatar_path">`

このとき、コントローラー側で

`if ($request->hasFile('avatar_path')) {     // アップロード処理 }`

と書くと、リクエストの中に `avatar_path` というnameでファイルが送られているかどうかをチェックします(avatar_pathはカラム名)

- **`true`（真）**：ファイルがアップロードされている
    
- **`false`（偽）**：アップロードされていない
    

---

### よくある使い方

新しい画像がアップロードされた場合だけ処理したい場合：

```php

$user = user::firstOrNew(['user_id' => $user->id]);

//アップロードされたファイルがあれば
if ($request->hasFile('avatar_path')) { 
	//そのファイルオブジェクトをpublic/material_imagesに保存する 
	$path = $request->file('avatar_path')->store('material_images', 'public'); 
	//    
	$user->avatar_path = $path; 
}
```
逆に、ファイルがなかったらスキップするので、古い画像はそのまま残ります。

---

###処理の流れ

 1 `$request->file('avatar_path')`
    
    - リクエストの中から アップロードされたファイルを取得。
    - ここで返ってくるのは ファイルオブジェクト（`UploadedFile` クラスのインスタンス）です。
        
 2 `->store('material_images', 'public')`
    
    - 取得したファイルを `storage/app/public/material_images` に保存します。
    - // '第１引数のmaterial_images'は保存先ディレクトリ、第２引数'public'はstorage/app/public
        
    - つまり `storage` ディレクトリの `app/public` 以下に `material_images` フォルダが作られ、その中にファイルが入ります。
    - 返り値は `material_images/xxxxxx.jpg` のような 相対パス文字列。
        
 3 変数 `$avatarPath` に格納
    
    - このパスをDBの `avatar_path` カラムに保存しておけば、ビュー側でそのパスを使って画像を表示できるようになります。
    
---



### 注意点

- `<form>` タグに `enctype="multipart/form-data"` が付いていないと `hasFile()` は常に `false` になります。
    
- フォームの `name` 属性と一致しないキーを渡した場合も `false`。