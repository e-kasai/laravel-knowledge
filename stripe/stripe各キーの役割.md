
---

#### stripe各キーの役割

- .env と config/services.php に書くのは「Stripeの認証情報をLaravelに知らせる」ため。
- 公開キー (STRIPE_KEY) はフロント（JavaScript側）が使う。
- 秘密キー (STRIPE_SECRET) はサーバ側で支払い処理を作成する時に使う。
- Webhookシークレット (STRIPE_WEBHOOK_SECRET) はStripe → Laravelに通知が飛んできた時に「これはStripe本人から」と確認するために使う。
