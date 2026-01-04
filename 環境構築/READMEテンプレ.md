
---
# アプリケーション名

### xxx

---

## 概要

- ショップ利用ユーザー対象のお問い合わせフォーム機能

- ログイン済み管理者による問い合わせデータ管理機能

---

## 環境構築

### Docker ビルド

1. git clone
```bash
git clone SSHURL // SSHの場合はこちら
git clone HTTPS URL // HTTPSの場合はこちら
```


2. Docker立ち上げ
```bash
cd xxx
docker-compose up -d --build
```



### Laravel 環境構築

1. `docker-compose exec php bash`

2. `composer install`

3. `docker compose exec php cp .env.example .env`

4. `.env`に以下の環境変数を追加

``` text
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=laravel_pass
```

5. アプリケーションキーの作成

``` bash
php artisan key:generate
```
  
6. シンボリックリンクを貼る（初回 & デプロイ時に実行）
```
php artisan storage:link
```

7. マイグレーションの実行

``` bash
php artisan migrate
```

  
8. シーディングの実行

``` bash
php artisan db:seed
```


### 環境依存について

 MacのM1・M2チップのPCの場合、`no matching manifest for linux/arm64/v8 in the manifest list entries`のメッセージが表示されビルドができないことがあります。

エラーが発生する場合は、docker-compose.ymlファイルの「mysql」セクションに「platform」の項目を追加で記載してください

``` bash
mysql:
    platform: linux/x86_64(この文追加)
    image: mysql:8.0.26
    environment:
```

---
## 使用技術

- Laravel 8.83.29

- PHP 7.4.9

- MySQL 8.0.26

- Docker/docker-compose
---

## ER 図

![ER図](./docs/er.png)

---

## URL

- 開発環境：http://localhost/

- phpMyAdmin：http://localhost:8080/

