
---

#### CheckoutController でstripe サーバーに送るrequestを定義

app/Http/Controllers/CheckoutController.php

1. webhook なしver (購入押したらただstripeに遷移すればOK)
```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Item;
use Stripe\StripeClient; //StripeClientのAPIクラス Stripeさんこれこれのセッション作っての窓口役

class CheckoutController extends Controller
{
    public function checkout(Request $request, Item $item)
    {
// $sessionはマイグレーションとかDBとは無関係で、Stripe のサーバに送るリクエストオブジェクト
// だからDBにカラムはなくていい
	$session = $stripe->checkout->sessions->create([
	    'mode' => 'payment',
	    'payment_method_types' => ['card', 'konbini'],
	    'line_items' => [[
	        'price_data' => [
			'currency' => 'jpy',
            'product_data' => ['name' => $item->item_name],
            'unit_amount' => (int) $item->price,
	        ],
        'quantity' => 1,
    ]],
    'success_url' => route('details.show', $item), // 支払い完了した人用の出口
    'cancel_url'  => route('details.show', $item), // やっぱやめた人用の出口 これがないとstripe画面から出られない
	]);

	// urlはstripeが返すレスポンスの中にあるキー
	// $session->urlはstripeのcheckout URL
	// ユーザーがそこにアクセスすると「カード番号入力」や「コンビニ支払い用の番号発行」画面が出る
	return redirect($session->url);
	}
}

```

---
#### stripe clientクラスとは

* 自分のアプリとstripeサーバーのAPI（API = Application Programming Interface アプリ同士が会話する窓口）

```php
$stripe = new StripeClient('sk_test_xxx'); // APIキーで初期化 これで $stripe が「Stripe APIと話せるオブジェクト」になる
```

** 何ができるの？

$stripe を使うと、Stripe のいろんな機能にアクセスできるようになる。
例：

* Checkout セッションの作成
$stripe->checkout->sessions->create([...]);

* 顧客(Customer)の作成
$stripe->customers->create([...]);

* 支払いIntentの確認
$stripe->paymentIntents->retrieve('pi_xxx');

---

#### $sessionの各キーの意味

* mode
セッションの種類。今回は一度きりの支払いなので 'payment'。
他にもサブスク用の 'subscription' がある。

* payment_method_types
使える支払い方法。
'card'（カード払い）、'konbini'（コンビニ払い）を有効化してる。

* line_items
いわゆる「カートの中身」。商品ごとに金額や数量を指定する。

- price_data → 価格情報の詳細
- currency : 通貨（jpy で日本円）
- product_data : 商品の表示名（ここでは $item->item_name）
- unit_amount : 単価（Stripeは日本円の場合そのまま整数円。1200円なら1200）
- quantity : 個数

---

#### コードの流れ

$stripe->checkout->sessions->create([...])
ここで「決済セッション」を Stripe 側に作成。

Stripe がレスポンスを返すと、その中に url というキーが含まれてる。
例:
https://checkout.stripe.com/c/pay/cs_test_a1b2c3d4...


$session->url
これは上の Stripe Checkout の専用URL。
ユーザーがそこにアクセスすると「カード番号入力」や「コンビニ支払い用の番号発行」画面が出る。

return redirect($session->url);
Laravelの redirect() ヘルパーで、そのURLにユーザーを飛ばす。
→ ブラウザは自動的に Stripe の決済画面に移動する。

---

#### まとめ

* 「決済用の一時的な専用ページをStripeに作らせ、そのURLへユーザーを飛ばす」
* Laravel側はただ「行ってらっしゃい」とリダイレクトしてるだけ、画面を描画してるわけじゃない。

---




```php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\Item;
// Stripe公式ライブラリの Stripe クラス
use Stripe\Stripe;
// Stripe\Checkout\Session というクラスを読み込んで、**別名（エイリアス）**として CheckoutSession を使う
use Stripe\Checkout\Session as CheckoutSession;

class CheckoutController extends Controller
{
    public function create(Request $request, Item $item)
    {
        $this->authorize('purchase', $item); // 自分の設計に合わせて（自分の出品は不可 等）
        if ($item->is_sold) {
            return back()->with('message', 'この商品は売り切れです。');
        }

        Stripe::setApiKey(config('services.stripe.secret', env('STRIPE_SECRET')));

        // ユーザーの事前選択があれば反映、なければ両方許可
        $pm = $request->string('pm')->toString();
        $allowed = match ($pm) {
            'card'    => ['card'],
            'konbini' => ['konbini'],
            default   => ['card','konbini'],
        };

        $session = CheckoutSession::create([
            'mode' => 'payment',
            'payment_method_types' => $allowed,   // カード/コンビニ
            'line_items' => [[
                'price_data' => [
                    'currency' => 'jpy',
                    'unit_amount' => $item->price, // 税込価格に合わせる
                    'product_data' => [
                        'name' => $item->item_name,
                    ],
                ],
                'quantity' => 1,
            ]],
            'client_reference_id' => (string)$item->id, // Webhookで商品を特定するため
            'success_url' => route('details.show', $item) . '?paid=1',
            'cancel_url'  => route('details.show', $item) . '?canceled=1',

            // Checkoutで氏名/メール/電話を集める（Konbiniは氏名・メールが重要）
            'customer_creation' => 'always',
            'phone_number_collection' => ['enabled' => true],
        ]);

        return redirect()->away($session->url);
    }
}
```

client_reference_id に item_id を入れておくと、Webhookでどの商品か即わかって便利。
CheckoutはサーバでSession作成→URLにリダイレクトが基本様式よ。