
---
#### MailHog を docker-compose に追加

**実行ディレクトリ:** プロジェクトのルート（`docker-compose.yml` がある場所）

```php
# docker-compose.yml に追記（services の並びに）
  mailhog:
    image: mailhog/mailhog:v1.0.1
    ports:
      - "1025:1025"   # SMTP受信
      - "8025:8025"   # Web UI
```

* ymlファイルの構文エラーがないか確認
```bash
# 実行ディレクトリ：プロジェクトのルート
docker compose config
```

* 反映
```bash
# 実行ディレクトリ：プロジェクトのルート 
docker compose up -d
```

ブラウザで `http://localhost:8025` を開ければOK。



ymlファイル
```php
version: '3.8'

services:
  mailhog:
    image: mailhog/mailhog:v1.0.1
    ports:
      - "1025:1025"   # SMTP受信
      - "8025:8025"   # Web UI
  nginx:
    image: nginx:1.21.1
    ports:
      - "80:80"
    volumes:
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf
      - ./src:/var/www/
    depends_on:
      - php

  php:
    build: ./docker/php
    volumes:
      - ./src:/var/www/

  mysql:
    image: mysql:8.0.26
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel_db
      MYSQL_USER: laravel_user
      MYSQL_PASSWORD: laravel_pass
    command:
      mysqld --default-authentication-plugin=mysql_native_password
    volumes:
      - ./docker/mysql/data:/var/lib/mysql
      - ./docker/mysql/my.cnf:/etc/mysql/conf.d/my.cnf

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      - PMA_ARBITRARY=1
      - PMA_HOST=mysql
      - PMA_USER=laravel_user
      - PMA_PASSWORD=laravel_pass
    depends_on:
      - mysql
    ports:
      - 8080:80

```