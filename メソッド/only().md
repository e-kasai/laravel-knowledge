## $request->only()

### 使い方
```php
$request->only(['name', 'email']);
```

### 意味
フォーム送信データのうち指定キーだけ取り出す

### 注意
DBのカラム名と一致しないとエラー