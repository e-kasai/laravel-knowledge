
---

実行ディレクトリ：/src

app/Http/Controllers/StripeWebhookController.php

```php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use App\Models\Item;
use App\Models\Transaction;
use Stripe\Stripe;
use Stripe\Webhook;

class StripeWebhookController extends Controller
{
    public function handle(Request $request)
    {
        $payload = $request->getContent();
        $sigHeader = $request->header('Stripe-Signature');
        $secret = env('STRIPE_WEBHOOK_SECRET');

        try {
            $event = Webhook::constructEvent($payload, $sigHeader, $secret);
        } catch (\Throwable $e) {
            return response('Invalid', 400);
        }

        $type = $event->type;
        $data = $event->data->object ?? null;

        // Checkoutのセッションから item_id を取り出す
        $session = $data;
        $itemId  = (int)($session->client_reference_id ?? 0);

        // 「成功」をハンドル（カードはcompleted、コンビニはasync_payment_succeeded）
        if (in_array($type, [
            'checkout.session.completed',
            'checkout.session.async_payment_succeeded',
        ], true)) {
            DB::transaction(function () use ($itemId, $session) {
                $locked = Item::whereKey($itemId)->lockForUpdate()->firstOrFail();
                if ($locked->is_sold) {
                    return; // 二重防止
                }
                Transaction::create([
                    'item_id'              => $locked->id,
                    'buyer_id'             => auth()->id() ?? null, // 任意：未ログイン購入設計ならnull許容
                    'purchase_price'       => $locked->price,
                    'payment_method'       =>  $session->payment_method_types[0] ?? 0, // 保存の仕方はお好みで
                    'shipping_postal_code' => null, // 既存プロフ/フォームから運ぶなら埋める
                    'shipping_address'     => null,
                    'shipping_building'    => null,
                ]);
                $locked->update(['is_sold' => true]);
            });
        }

        // 失敗（コンビニ未入金期限切れ等）
        if ($type === 'checkout.session.async_payment_failed') {
            // 必要があれば「保留ステータス解除」等の処理
        }

        return response('OK', 200);
    }
}
```


Webhookで聴くべきは checkout.session.completed（カード） と
checkout.session.async_payment_succeeded（コンビニ入金完了）、
失敗は ...async_payment_failed。

Stripe公式がこれらのイベントを定義している。