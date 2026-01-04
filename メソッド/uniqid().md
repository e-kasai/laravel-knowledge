
---

- 実行するたびに一意な文字列（タイムスタンプベースのID）を返す

#### image
```php
uniqid(); // "65020f6c9c3b2" みたいな文字列

//これをメールアドレスの前に付け足すと
$email = 'test' . uniqid() . '@example.com';
// => "test65020f6c9c3b2@example.com"
```

