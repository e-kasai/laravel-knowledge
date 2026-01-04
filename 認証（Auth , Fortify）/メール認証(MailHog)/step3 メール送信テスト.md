
---

```bash
# 実行ディレクトリ: プロジェクトのルート
docker compose exec php php artisan tinker

\Mail::raw('テストメール本文', function ($message) {
    $message->to('dummy@example.com')->subject('テスト送信');
});
```

MailHog の `http://localhost:8025` に届くか確認