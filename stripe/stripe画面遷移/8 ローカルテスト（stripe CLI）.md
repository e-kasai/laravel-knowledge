
---

実行ディレクトリ：ローカルホスト側（CLIが動く場所）

1. 受け口をDockerのPHP→nginx経由で外に出せているなら、そのURLを指定
stripe listen --forward-to http://localhost/stripe/webhook

2. 重要イベントを流してみる

* stripe trigger checkout.session.completed
* stripe trigger checkout.session.async_payment_succeeded


これでWebhookが届いてis_sold更新の確認ができる

---

補足：なぜCheckoutを使うの？

* 早い・安全：カード情報やコンビニ番号発行フローをStripeが全部持つ
* Konbini要件（氏名・メール等）や支払い期限などの面倒を吸収
* ダッシュボードで支払い方法ON/OFFも即切替できる 