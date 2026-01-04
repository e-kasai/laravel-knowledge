
---

#### StripeClientとは？

API = Stripe本社にある受付窓口
StripeClient = その窓口に話しかける「通訳さん」


例えるなら…

Stripe = 銀行やクレジット会社
API = 「銀行に振込依頼する窓口」
StripeClient = その窓口に話しかけるための「職員さん（窓口係）」

---

#### どうやって使うか？

まずは new で作る：

```php
use Stripe\StripeClient;
$stripe = new StripeClient('シークレットキー');
```

これで $stripe という「Stripeへの窓口」が手に入る。

---
#### そこからできること

支払いセッション作成
$stripe->checkout->sessions->create([...]);
→ 「Stripeの決済ページを作って！」と依頼

顧客の作成
$stripe->customers->create([...]);

返金
$stripe->refunds->create([...]);

---

#### Laravelでの流れ

.env に Stripe の秘密鍵を置く
`STRIPE_SECRET=sk_test_1234...`

`config/services.php` にキーを登録

```php
'stripe' => [
    'secret' => env('STRIPE_SECRET'),
],
```


その秘密鍵を使って
```php
//コントローラーで
new StripeClient(config('services.stripe.secret'))
```

で作る。
これが StripeClient という「Stripe窓口」クラス。

---
#### まとめ

StripeClient = Stripe公式のAPIを呼ぶためのクラス
Laravel内で new StripeClient(...) すると、そのインスタンスを通じて支払い処理ができる