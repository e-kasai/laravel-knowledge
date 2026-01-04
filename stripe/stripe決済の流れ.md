
---

### Stripe決済の基本の流れ（Checkoutを使う場合）

1. クライアント（ユーザー）が「購入」ボタンを押す

あなたのLaravelアプリから「支払い開始リクエスト」がサーバーに送られる。
ここで「どの商品」「いくら」「支払い方法（カード or コンビニ）」などを指定。

---

2. サーバー側でCheckout Sessionを作成

StripeClient を使って checkout.sessions.create() を呼び出す。
そのときに渡すもの：

payment_method_types（例：['card'] や ['konbini']）
line_items（商品名、金額、数量）
mode（支払い or 定期課金）
success_url（成功時に戻るURL）
cancel_url（キャンセル時に戻るURL）

👉 戻り値として sessionの情報 が返ってきて、その中に session->url がある。

---

3. ユーザーをStripeの決済ページへリダイレクト

return redirect($session->url); とするだけでStripeの画面へ飛べる。
そこでユーザーはカード情報やコンビニ選択を入力。

---

4. 決済処理

カード決済：即時で支払い完了。
コンビニ決済：ユーザーは払込票を持ってコンビニで支払う → その場では「Pending（保留）」。

---

5. 成功後のリダイレクト

ユーザーが決済を完了すると、success_url に戻ってくる。
ただし、この時点では「フロントに戻った」だけで、本当に支払われたかは未確定。

---

6. Webhookで確定

Stripeは「支払いが完了したよ」というイベントをサーバーに送ってくれる（Webhook）。
これを受け取って、DBに「購入完了」「SOLD」などを保存するのが本番的な正しい流れ。
（模擬案件ならWebhookを省略して「戻った時点で完了扱い」にする場合もある）

---
#### まとめ

ユーザー: 「購入」クリック
↓
Laravel: Stripeにセッション作成依頼
↓
Stripe: セッションURL返却
↓
Laravel: ユーザーをそのURLにリダイレクト
↓
ユーザー: Stripe画面で決済
↓
Stripe: 成功したら success_url に戻す
↓
Laravel: （Webhookで最終確定 or 簡易的にSOLD更新）