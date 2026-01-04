

```yml
nginx:
    image: nginx:1.21.1
    ports:
      - "80:80"  
    volumes:
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf  
      - ./src:/var/www/  
    depends_on:   
      - php
```

---

# ports

ホストの80番 → コンテナの80番につなぐ という意味
左側：ホスト側のポート　　＝ ブラウザでアクセスする番号
右側：コンテナ側のポート　＝　nginx が待ち受けている番号

つまり、	
http://localhost:80	　でアクセスすると、nginx コンテナの 80 番に届くよう設定している

---

# volumes (default.confの設定）

ホスト側にある　`./docker/nginx/default.conf`を
コンテナ内の 　　`/etc/nginx/conf.d/default.conf`　に上書きして使わせるよう指定している

## 説明

1. nginx公式イメージには最初から default.conf（/etc/nginx/conf.d/default.conf） がある
2. それを人が確認する
```bash
docker exec -it nginx cat /etc/nginx/conf.d/default.conf
```
（または公式ドキュメントを見る）

3. 内容を見ながら公式をベースにしてホスト側に人が設定ファイルを作る
`docker/nginx/default.conf`

4. docker-compose.yml の volumes で「これを使え」と指定する
  （volumesでコンテナ内の default.conf（nginx公式）を上書きマウント　→ ホスト側のファイルを編集したらブラウザ更新で即反映される）
  
```yml
# 左がホスト側、右がコンテナ側
- ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf  
```

結果
→　nginx は公式のファイルではなくホスト側の設定を読む

### なぜわざわざこうするのか?

#### アプリ用の設定に変えたいから
公式の default.conf は「誰にでも当てはまる最低限設定」

Laravel のように
- PHP に処理を渡す
- /public を root にする
- fastcgi を使う

などのアプリ用の設定が入ってないので入れる必要がある

#### point : 設定ファイルの場所はホスト
ファイルの場所
```bash
プロジェクトルート/
└ docker/
   └ nginx/
      └ default.conf   ← 人が作るファイル
```
※ホスト側に置く　＝　Gitで管理できる＆コンテナを消しても消えない
　　　　　　　　　　　＝　nginx の設定を変えたいときにコンテナを再作成しないでいい

---

# depends_on

```yml
depends_on:   
      - php
```

### 起動順を指定

nginx は php が起動してから起動する
nginxだけ先に起動するとPHPの処理を、phpコンテナに渡す設定（fastcgi）が間に合わないから


# volumes: ./src:/var/www/ 

```yml
- ./src:/var/www/
```

### 意味

- **左（ホスト側）**  
  `./src`  
  → Laravel のソースコードがある場所

- **右（コンテナ側）**  
  `/var/www/`  
  → nginx / php がアプリを見る場所

つまり  
**ホストの src を、コンテナ内の /var/www として使わせる**指定。

---

### なぜ必要か

#### nginx 側
nginx の設定では、よくこう書かれます。

```nginx
root /var/www/public;
```
→ nginx は `/var/www/public` を配信元として見る

#### php 側
`php-fpm` も `/var/www` を基準に  `index.php` などを実行する

---

### これがないとどうなる？

- nginx から見て `/var/www` が空
- php から見ても Laravel のコードがない

結果として：
- 404
- PHP が動かない
- 画面が表示されない


### なぜ nginx と php の両方に書く？

nginx と php は 別コンテナだから。
同じコードを見るために、  両方に同じ volume をマウントする必要がある

```yml
nginx:
  volumes:
    - ./src:/var/www/

php:
  volumes:
    - ./src:/var/www/
```


