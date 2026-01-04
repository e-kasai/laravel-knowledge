
---

実行ディレクトリ：/src

app/Http/Middleware/VerifyCsrfToken.php にWebhookパスを除外

```php
protected $except = [
    'stripe/webhook',
];
```

署名検証は上のコードで constructEvent が実施（STRIPE_WEBHOOK_SECRET 使用）
