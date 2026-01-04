
---


## 1. コンテナ名の一致（最重要）

MySQLの接続先は **4か所すべて一致** している必要がある。

- `.env` → `DB_HOST`
- `docker-compose.yml` → `container_name`（mysql）
- `phpmyadmin` の `PMA_HOST`
- `docker ps` の NAMES 列に表示される実コンテナ名

**例：**

```
DB_HOST=contact-form-mysql
```
```
container_name: contact-form-mysql
```

---

## 2. .env に標準キーを使っている & config/database.php が標準のまま

### ✔ database.php は標準キーを読む前提で書かれている

```
'mysql' => [
    'driver' => 'mysql',
    'url' => env('DATABASE_URL'),
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '3306'),
    'database' => env('DB_DATABASE', 'forge'),
    'username' => env('DB_USERNAME', 'forge'),
    'password' => env('DB_PASSWORD', ''),
    'unix_socket' => env('DB_SOCKET', ''),
]
```

### ✔ だから .env でも標準キーを使わないと参照されない

```
DB_DATABASE=contactform_db
DB_USERNAME=contactform_user
DB_PASSWORD=contactform_pass
```

---

## 3. .env の値が docker-compose.yml と一致しているか

docker-compose.yml:

```
environment:
  MYSQL_ROOT_PASSWORD: root

  MYSQL_DATABASE: contactform_db
  MYSQL_USER: contactform_user
  MYSQL_PASSWORD: contactform_pass
```

.env:

```
DB_CONNECTION=mysql
DB_HOST=contact-form-mysql
DB_PORT=3306

DB_DATABASE=contactform_db
DB_USERNAME=contactform_user
DB_PASSWORD=contactform_pass
```

**→ 3つ（DB / USER / PASS）が一致していればOK**

---

## 4. volumes が正しく設定されているか（永続化）

MySQL の本体データが `/var/lib/mysql` にマウントされていること。

docker-compose.yml:

```
volumes:
  - ./docker/mysql/data:/var/lib/mysql
  - ./docker/mysql/my.cnf:/etc/mysql/conf.d/my.cnf
```

---

## 5. mysql_native_password が有効か

MySQL 8 はデフォルトで `caching_sha2_password` になっているため  
Laravel からログインできない場合がある。

docker-compose.yml:

```
command:
  mysqld --default-authentication-plugin=mysql_native_password
```

**→ これがないと接続エラーになることがある**

---

##  container_name を設定しているか（推奨）

docker-compose.yml：

```
container_name: contact-form-mysql
```

自動付与だと `project-mysql-1` のように  
`docker compose down` のたびに名前が変わり、  
`.env → DB_HOST` とズレて接続不能になるため。

---