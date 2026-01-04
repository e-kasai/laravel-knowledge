
---

#### ngrokってなに

- ngrokは「トンネル」を作るツール。
- Stripeから見ると インターネット上の公開URL（例: https://xxxx-1234.ngrok.io/stripe/webhook）が生まれる。
- その公開URLに来たリクエストを、ngrokがじぶんのローカル環境（localhost:8000 とか）へ転送してくれる。
- これでStripeは「外の住所」を使ってWebhookを送れて、自分のローカルLaravelアプリが受け取れる。


#### 全体の流れ

1. ngrokで https://xxxx.ngrok.io/stripe/webhook を作成
2. StripeダッシュボードでこのURLを登録
3. 発行された whsec_xxx を .env に設定

---
#### ngrok インストール
 
```bash
// UbuntuのどこからでもOK
// ダウンロード＆セットアップ
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | \
  sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null

echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | \
  sudo tee /etc/apt/sources.list.d/ngrok.list

sudo apt update
sudo apt install ngrok
//インストール確認
ngrok version
```

---

#### ngrokの認証

1. ngrokのアカウントを作る

ブラウザで https://ngrok.com/
無料アカウントを作成（Google / GitHub 連携でもOK）

2. Auth Token を確認する

ログインしたら ngrok のダッシュボード（トップページ）。
画面上部に Your Authtoken という欄があるのでコピー

![[Pasted image 20250916112810.png]]

3. ngrok.ymlに登録：
```bash
ngrok config add-authtoken <Your AuthToken>
//成功すると ~/.config/ngrok/ngrok.yml に保存されるので、次回以降は入力不要
```

4. 動作確認

```bash
ngrok http 80  //Dockerで nginx が ポート80 を公開してるから、そのまま指定できる
```

![[Pasted image 20250916113113.png]]

- `Session Status: online` → ngrokが正常に接続できてる
- Forwarding に
`https://386dd2bb002a.ngrok-free.app -> http://localhost:80`
って出てる → これが Stripe からアクセスできる外部URL

---

#### Stripe ダッシュボードに登録

1. Stripe管理画面 → Developers → Webhooks → 画面中央にある 「＋送信先を追加する」 ボタン

エンドポイントURLに登録するのはこれ
`https://386dd2bb002a.ngrok-free.app/stripe/webhook`
** 最後に設定したルートを忘れずに付ける！Laravelのルートに合わせる **

2. イベントを選択

- checkout.session.completed（必須）
- 追加で payment_intent.succeeded など必要に応じて[[登録すべきイベント]]

![[Pasted image 20250916114232.png]]

3. 保存
→ Signing secret（whsec_xxx）が出る

4. secretkey を`.env` に追記

`STRIPE_WEBHOOK_SECRET=whsec_xxx`

5. キャッシュをクリア
```bash
docker compose exec php php artisan config:clear
```

---

これで Stripe → ngrok → Laravel にWebhookが届くようになる。





