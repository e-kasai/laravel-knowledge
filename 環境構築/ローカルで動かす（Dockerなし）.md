
自分のPCに入っている PHP と MySQL を使って Laravel を動かす
「ローカルで動かす」＝ `php artisan serve` で自分のPC上でLaravelを起動すること

# 必要なもの（最低限）

ターミナル（PowerShell / コマンドプロンプト）で確認
- PHP（php -v）
- Composer（composer -V）
- MySQL（mysql --version）



# 1. プロジェクトに入る
```
cd restapipj // ex
```

# 2. .env を設定
```
// 以下はあくまで例
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=restapipj
DB_USERNAME=root
DB_PASSWORD=root
```


# 3. マイグレーション & シード

```
php artisan migrate
php artisan db:seed
```
テーブルとダミーデータが入る


# 4. サーバーを起動

```
php artisan serve
```
Laravelが動く

# 5. APIにアクセス

```
GET http://127.0.0.1:8000/api/v1/rest
```
POST / PUT / DELETE  → Postman や curl で叩く