
---

実行ディレクトリ：/src

routes/web.php

```php
use App\Http\Controllers\CheckoutController;
use App\Http\Controllers\StripeWebhookController;

Route::post('/checkout/{item}', [CheckoutController::class, 'create'])
    ->name('stripe.checkout.create')
    ->middleware('auth');

//webhook 使用する場合のみ以下は書く
Route::post('/stripe/webhook', [StripeWebhookController::class, 'handle'])
    ->name('stripe.webhook'); // CSRF除外はVerifyCsrfTokenで設定
```

---

** webhookとは **
Webhook（ウェブフック）は、Stripe側からアプリに「決済が完了したよ！」って通知を送ってくる仕組み

例：
* checkout.session.completed → ユーザーが決済を完了した
* payment_intent.succeeded → 支払いが確定した

Laravel側でこれを受け取って、

* DBに「取引レコード」を作る
* 在庫を「売切れ」に更新する
* ユーザーに「購入完了メール」を送る

といった後処理を実行する