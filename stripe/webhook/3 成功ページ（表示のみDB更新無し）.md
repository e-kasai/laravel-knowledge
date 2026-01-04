
---

- DB更新はしない。
- Stripe Checkout の決済完了確認ページを表示する処理
- カードは成功表示、コンビニは「支払い待ち」を見せるだけ
---

`app/Http/Controllers/CheckoutController.php`

```php
use Stripe\Stripe;
use Stripe\Checkout\Session as StripeSession;

public function show(Request $request)
{
	//session_id が URL パラメータにあるか確認
    $sessionId = $request->query('session_id');
    //なければ 400 Bad Request を返して処理を止める
    abort_unless($sessionId, 400);

	//.env に保存してある 秘密鍵（secret key） を使って Stripe API を操作できるようにする
    Stripe::setApiKey(config('services.stripe.secret'));
    
    //session_id を指定して Checkout セッション情報を取得
    //expand: ['payment_intent'] を指定=関連する PaymentIntent（支払い情報の本体） も一緒に展開
    //これをしないと $session->payment_intent に id しか入らない
    $session = StripeSession::retrieve(['id' => $sessionId, 'expand' => ['payment_intent']]);

	//支払い完了判定
    $isPaid = $session->payment_status === 'paid' //条件1: Checkout セッション全体が paid になっている。
           && $session->payment_intent?->status === 'succeeded';  //条件2: PaymentIntent のステータスが succeeded

	//$isPaid が true なら checkout/success.blade.php を表示。
	//そうでなければ checkout/pending.blade.php を表示。
    return view($isPaid ? 'checkout.success' : 'checkout.pending', ['session' => $session]);
}
```

---

