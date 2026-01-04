---
tags:
  - "#ミドルウェア"
---

auth ミドルウェア
→ 「ログイン済みかどうか」をチェックするフィルター。
未ログインなら /login にリダイレクトされる。

コードの中身

Illuminate\Auth\Middleware\Authenticate に書かれてる。

```php
protected function redirectTo($request)
{
    if (! $request->expectsJson()) {
        return route('login'); // 未ログインなら login ルートへ
    }
}
```


handle() で「ユーザーがログイン済みか」チェックする

ログインしてなければ redirectTo() が呼ばれて、login ルートに飛ばす

---

#### 処理の流れ

リクエストが来る

auth ミドルウェアが $request->user() を確認

ユーザーがいなければ redirectTo() を実行

結果として /login にリダイレクト

---

#### まとめ

auth ミドルウェア = ログイン必須チェック
未ログインなら /login に飛ばす（login ルート）
これで「ログインユーザー専用ページ」を守ることができる