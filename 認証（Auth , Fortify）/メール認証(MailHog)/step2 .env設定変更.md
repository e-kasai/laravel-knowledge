
---
#### Laravel の .env を MailHog 用に設定

```php
// .env
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="test@example.com" //送信者のアドレスになる部分なんでもOK
MAIL_FROM_NAME="${APP_NAME}"         //送信者名として表示される部分 .envのAPP_NAMEの値が入る
```
** point **
Dockerネットワーク上のサービス名`mailhog`をHOSTにする

![[Pasted image 20250910121229.png]]

![[Pasted image 20250910121300.png]]


#### 送信者名の部分変更パターン

* APP_NAMEを変更
```php
APP_NAME="FleaMarket"
MAIL_FROM_NAME="${APP_NAME}"
```

アプリ全体で使われる名前が「FleaMarket」になる（ログやメールなど全部)

---

* MAIL_FROM_NAMEだけ書き換える
```php
APP_NAME="Laravel"
MAIL_FROM_NAME="FleaMarket"
```

アプリ名は Laravel のまま、メールの差出人だけ「FleaMarket」