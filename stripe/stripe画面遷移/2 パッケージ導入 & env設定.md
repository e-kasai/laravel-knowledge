
---

#### Stripe SDK のインストール

実行ディレクトリ：phpコンテナ内

```bash
composer require stripe/stripe-php
```

---

#### envに環境変数を設定

* .env に追記（値はダッシュボードのテストキー）

```
STRIPE_KEY=pk_test_xxx
STRIPE_SECRET=sk_test_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx   # 後でWebhook作成時に入れる
```

* 読ませるために、config/services.php に以下を追記する：

```php
'stripe' => [
    'secret' => env('STRIPE_SECRET'),
    'key'    => env('STRIPE_KEY'),
],
```
編集後、必要なら php artisan config:clear

こうしておくと、アプリ側で config('services.stripe.key') や config('services.stripe.secret') から呼べるようになる。

** つまりアプリから Stripe API を呼べるようになる **