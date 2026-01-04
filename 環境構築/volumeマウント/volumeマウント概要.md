
# ボリュームのマウントとは

コンテナを消してもデータが消えない様にする（データの永続化）ため、コンテナの中の保存場所を、コンテナの外の保存場所につなぐこと


# ボリュームのマウントの種類

## 1. ボリュームマウント
- Docker Engineが管理する領域内にボリュームを作成する
メリット
- 誤ってデータを消す事故が起きにくい
- OSによってパスが変わらない
（作成したコンテナを違うOSユーザーに配る時通常はパスを書き換える必要があるがそれが不要）

デメリット
- Docker Engineの管理下にある為、直接操作はしづらい
- バックアップ時などに複雑な手順が必要


## 2. バインドマウント
- ホストPCのドキュメントやデスクトップ等にコンテナをマウントする

メリット
- 普通のファイルと同じように簡単に操作が可能


# ボリュームマウントの手順

## 1. 記憶領域を作成する

- バインドマウントの場合
通常通りフォルダやファイルを作ればOK（Cドライブ直下やドキュメント内など）

- ボリュームマウントの場合
コマンドで作成　（どのディレクトリからでもOK）
```
docker volume create ボリューム名 // ボリュームの作成
docker volume rm ボリューム名    // ボリュームの削除
```

## 2. マウントする

### バインドマウント

docker-compose使わない場合
```
docker run -v ホスト側の記憶領域パス:コンテナの記憶領域パス
```
ymlファイルで指定する場合
```yml
volumes:
      - ./docker/mysql/data:/var/lib/mysql                    //MySQLのデータを保存する
      - ./docker/mysql/conf.d:/etc/mysql/conf.d:ro           //MySQLの設定ファイル（.cnf）を置く ro=read only
```
- 左側がホスト（自分のPC）
- プロジェクトごとに`mysql/data`というディレクトリが存在する　＝　プロジェクトごとにDBが完全分離している

### ボリュームマウント
```
docker run -v ボリューム名:コンテナの記憶領域パス(ソフトウェアの公開ディレクトリ）
```

ex:
```
docker run --name apa000ex21 -d \
  -p 8091:80 \
  -v apa000vol1:/usr/local/apache2/htdocs \
  httpd
```  

output
```
ekasai@EKPC:~/project_files$ docker run --name apa000ex21 -d \
  -p 8091:80 \
  -v apa000vol1:/usr/local/apache2/htdocs \
  httpd
722a2df90f08a5fcec43f9903a95073868132ba8ed34b2b5df00a3937c2c36fa    //これがコンテナID
ekasai@EKPC:~/project_files$ docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS          PORTS                                     NAMES
722a2df90f08   httpd     "httpd-foreground"   21 seconds ago   Up 21 seconds   0.0.0.0:8091->80/tcp, [::]:8091->80/tcp   apa000ex21
ekasai@EKPC:~/project_files$
```

## 3. マウントの確認


## 共通の考え方
- 「コンテナの中から、期待した場所が見えるか」を確認する
- それが見えれば **マウント成功**
- コンテナを消しても残れば **永続化成功**

### バインドマウントの場合(MySQLの例)

1. コンテナ内から確認
```
docker exec -it mysql-container ls /var/lib/mysql
```

DB名のフォルダ
ibdata1 などが見える → マウント成功

2. ホスト側から確認
```
ls ./docker/mysql/data
```
同じファイル群が見える →　マウント成功


### ボリュームマウントの場合

1. コンテナ内から確認
```
docker exec -it container-name ls マウント先パス
```
マウント先パス
```
Apache: /usr/local/apache2/htdocs
MySQL: /var/lib/mysql
```

→ ファイルが見える = マウント成功

2. ボリューム情報の確認
```
docker volume inspect ボリューム名
```
Mountpoint が表示される
Docker管理領域に保存されている → 永続化OK


3.  永続化できているかの確認

まずコンテナを削除
```
docker rm -f コンテナ名
```

そのあと、同じボリュームを使って再起動

point
- ボリューム名が同じ（mysql_data）
- 新しいコンテナ（mysql2）
```
docker run -d \
  --name mysql2 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
```  

再度コンテナ内から確認
```
// docker exec -it new-container ls マウント先パス
docker exec -it mysql2 ls /var/lib/mysql
```

データが残っている → 永続化成功