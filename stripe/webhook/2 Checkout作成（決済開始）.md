
---

商品・購入者情報をmetadataに入れておくと、Webhookで取り出せて便利

---
`app/Http/Controllers/CheckoutController.php`

```php
use Stripe\Stripe;
use Stripe\Checkout\Session as StripeSession;

public function create(Item $item)
{
    $this->authorize('purchase', $item);
    Stripe::setApiKey(config('services.stripe.secret'));

    $session = StripeSession::create([
        'mode' => 'payment',
        'payment_method_types' => ['card','konbini'], // カードのみなら['card']
        'line_items' => [[
            'quantity' => 1,
            'price_data' => [
                'currency' => 'jpy',
                'unit_amount' => $item->price * 100,
                'product_data' => ['name' => $item->item_name],
            ],
        ]],
        'success_url' => route('checkout.success').'?session_id={CHECKOUT_SESSION_ID}',
        'cancel_url'  => route('details.show', $item),
        'metadata'    => [
            'item_id'  => (string)$item->id,
            'buyer_id' => (string)auth()->id(),
        ],
    ]);

    return redirect()->away($session->url);
}
```

