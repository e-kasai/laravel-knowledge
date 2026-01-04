
---
- Webhookはローカルに届けないと動かない
- Stripe CLIで転送

---

実行ディレクトリ：プロジェクトルート

1 Stripeにログイン
```
stripe login
```

---
2 Webhook転送を開始

```
stripe listen --forward-to http://localhost:8000/stripe/webhook
```

---
3 アプリを起動（Docker/Sailでポート8000想定）

---
4 ダミーカードで購入

- カード成功：4242 4242 4242 4242（期限は未来、CVC任意）
- コンビニ：テスト手順に従って支払い完了イベントを発火
---
5 DB確認

- items.is_sold = true
- transactions に1件＋payment_intentユニーク

---