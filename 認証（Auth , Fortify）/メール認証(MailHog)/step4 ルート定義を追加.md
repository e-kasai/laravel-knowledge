---
#EmailVerificationRequest #verification #メール認証 
---

---
```php
//web.php

use Illuminate\Foundation\Auth\EmailVerificationRequest;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ItemController; 

// 未認証ユーザーに見せるメール認証案内ページ
Route::get('/email/verify', function () {
    return view('auth.verify_email');
})->middleware('auth')->name('verification.notice');


// メールのリンクを踏んだときの検証
Route::get('/email/verify/{id}/{hash}', function (EmailVerificationRequest $request) {
    $request->fulfill(); // verified_at をセット
    return redirect()->route('home'); // 認証後の遷移先
})->middleware(['auth', 'signed', 'throttle:6,1'])->name('verification.verify');


// 認証メールの再送
//長いと思ってもパスは変えないでdefualtのを使う
Route::post('/email/verification-notification', function (Request $request) { 
    $request->user()->sendEmailVerificationNotification();
    return back()->with('message', '認証メールを再送しました。');
})->middleware(['auth', 'throttle:6,1'])->name('verification.send');


// 商品一覧（認証済のみ閲覧可なら）
Route::get('/', [ItemController::class, 'index'])
    ->middleware(['auth', 'verified'])
    ->name('home');

```

#### 解説

** EmailVerificationRequest **
- Laravel が最初から用意してる特別な FormRequest クラス(自作不要)
- 役割は「メール認証用リンクが本物かどうかを検証すること」
- リンクの中にある `{id}` や `{hash}` をチェックし改ざんされたものでないかなどを検証
---

** $request->fulfill(); **
- ユーザーの `email_verified_at` カラムを現在時刻で更新して、「このユーザーはメール認証済み！」と記録するメソッド

---

** throttle:6,1 **
- アクセス制限（レート制限）をかけるミドルウェア
- 書式は `throttle:回数,分`。  
ここでは `6,1` だから「1分間に最大6回までしかアクセスできない」という意味。



