---
tags:
  - ミドルウェア
---
---
#### 役割

verified ミドルウェア
→ 「そのユーザーがメール認証済みかどうか」をチェックするフィルター。
メール認証してないユーザーは強制的に /email/verify にリダイレクトされる。

---

#### 仕組み
verified ミドルウェアの正体は Illuminate\Auth\Middleware\EnsureEmailIsVerified クラス。
この中で「メール認証済みか？」をチェックして、未認証ならリダイレクトする処理が書いてある。

`vendor/laravel/framework/src/Illuminate/Auth/Middleware/EnsureEmailIsVerified.php`

```php
public function handle($request, Closure $next, ...$guards)
{
    if (! $request->user() ||
        ($request->user() instanceof MustVerifyEmail &&
        ! $request->user()->hasVerifiedEmail())) {
        return $request->expectsJson()
                ? abort(403, 'Your email address is not verified.')
                : redirect()->route('verification.notice');
    }

    return $next($request);
}
```

1. $request->user() が存在するか確認（ログイン済みかどうか）
2. そのユーザーが MustVerifyEmail を実装していて、まだ hasVerifiedEmail() がfalseなら
3. JSONリクエストなら 403エラー を返す
4. それ以外（ブラウザアクセス）なら redirect()->route('verification.notice') が実行される

**verification.notice ルートとは？**

Laravelに最初から用意されてる名前付きルート
verification.notice という名前で登録されていて、表示画面は /email/verify
FortifyやUIパッケージを入れると、このルートが「認証してね」画面を表示する