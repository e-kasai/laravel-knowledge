
---

#### Makefileとは
Makefileとは「複数の処理（コマンド）をまとめて、自動的に順番どおり実行できるファイル」

#### .batとの違い
バッチファイル（.bat）は完全にWindows専用で、Linuxでは動かない。
MakefileはUNIX系の標準ツールだから、Mac・Linux・WSLでそのまま動く

---

#### 呼び出し方
実行場所：プロジェクト直下（＝Makefile が置いてあるディレクトリ）
実行環境：Ubuntu（WSLを含むLinux環境）

実行コマンド：
make init     ←init の部分は好きな名前でもいい（ex:setupとかなんでも）

---

#### 挙動
呼び出したターゲット名で上から順に実行される
※下記のコード例ではinit:とfresh:のみ実行される
理由：init:の最後で@make freshと呼び出しているのでfresh:は実行されるが他は呼び出されてないので実行されない
@ をつけると、「コマンドを実行するときに、ターミナルに表示しない」

#### 実行されないターゲットが書かれてる理由
"make ターゲット名"で短いコマンドで必要な処理が実行できる為
例：キャッシュをクリアしたい→"make cache"だけで済むので便利

---

#### code例

```bash
init:                                                                //ターゲット名 = init
	docker-compose up -d --build                                     //コマンド行の先頭は必ずインデントする
	docker-compose exec php composer install                         //PHPコンテナの中でcomposer install
	docker-compose exec php cp .env.example .env                     //PHPコンテナの中で .env.example から .env をコピー
	mkdir ./src/storage/app/public/img                               //ユーザーがアップロードする画像フォルダ"img"を作成
	
	//copy_storage_img にある.jpgたちを全部storage/app/public/img に移動
	mv ./src/public/img/copy_storage_img/*.jpg ./src/storage/app/public/img 
	
	docker-compose exec php php artisan key:generate                 //APPキーの作成
	docker-compose exec php php artisan storage:link                 //シンボリックリンクの作成
	docker-compose exec php chmod -R 777 storage bootstrap/cache     //書き込み権限を付与（キャッシュやログでエラーを防ぐ）
	@make fresh                                                      //「fresh」呼び出し

fresh:
	docker compose exec php php artisan migrate:fresh --seed         //DBをまっさらにしてマイグレーション＋シーディング実行

// 以下ターゲットは呼び出しされた場合の未実行される

// コンテナのリスタート（変更などがあった時クリーンにして再起動）
restart:
	@make down
	@make up

up:
	docker-compose up -d //差分反映（再起動または再ビルド）

down:
	docker compose down --remove-orphans    //ymlに定義されてない孤児コンテナを削除            

cache:
	docker-compose exec php php artisan cache:clear 
	docker-compose exec php php artisan config:cache 
	
	
stop:
	docker-compose stop
```

---

#### よくあるターゲット名と内容

| ターゲット | 内容 |
|-------------|----------------------------------------------|
| make init   | 初回環境構築（ビルド、envコピー、migrateなど） |
| make fresh  | DBをリセットしてシード |
| make up     | Docker起動 |
| make down   | Docker削除 |
| make test   | Laravelのテスト実行 |
| make cache  | キャッシュクリア |