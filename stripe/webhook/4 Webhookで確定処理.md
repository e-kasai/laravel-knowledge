
---

- イベントを検証→在庫確定→取引レコード作成
- 二重購入は行ロックとユニーク制約で防ぐ

---
`app/Http/Controllers/StripeWebhookController.php`

```php
use Illuminate\Support\Facades\DB;
use Stripe\Webhook;

class StripeWebhookController
{
    public function handle(Request $request)
    {
        $payload = $request->getContent();
        $sig = $request->header('Stripe-Signature');
        $secret = config('services.stripe.webhook_secret');

        try {
            $event = Webhook::constructEvent($payload, $sig, $secret);
        } catch (\Throwable $e) {
            return response('Invalid', 400);
        }

        if ($event->type === 'payment_intent.succeeded') {
            $pi = $event->data->object; // \Stripe\PaymentIntent
            $itemId  = (int)($pi->metadata->item_id ?? 0);
            $buyerId = (int)($pi->metadata->buyer_id ?? 0);

            if ($itemId && $buyerId) {
                DB::transaction(function () use ($pi, $itemId, $buyerId) {
                    $item = \App\Models\Item::lockForUpdate()->findOrFail($itemId);

                    if (!$item->is_sold) {
                        $item->update(['is_sold' => true, 'buyer_id' => $buyerId]);
                    }

                    \App\Models\Transaction::firstOrCreate(
                        ['payment_intent' => $pi->id], // ユニーク制約
                        [
                            'item_id'  => $itemId,
                            'buyer_id' => $buyerId,
                            'amount'   => $pi->amount, // JPYは最小単位=1円
                            'provider' => 'stripe',
                            'status'   => 'paid',
                        ]
                    );
                });
            }
        }

        return response('OK', 200);
    }
}
```
