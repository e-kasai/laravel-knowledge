
---

# 前提：絶対禁止事項

1.  **.gitignore 作成前に git add .**　    →  add . を先にやったら、もう ignore は効かない
2.  **clone元に紐づいたままpush**　      →　clone元に自分の変更がpushされる


---

##  STEP 1：git clone

```
//cloneを置きたいディレクトリに移動
cd xxx

// リポジトリを任意のフォルダ名でcloneする
// git clone "clone元URL" 半角スペース "好きなプロジェクトフォルダ名"
git clone git@github.com:Estra-Coachtech/laravel-docker-template.git attendance_system [^1]    // ←例

// プロジェクトフォルダに移動する
cd attendance_system
```

---

##  STEP 2： .git を削除（重要）

エクスプローラーで該当のプロジェクトフォルダを開いて.gitを削除する

理由：clone 直後のフォルダには、clone元の Git の履歴・設定・remote が入っている。
このまま作業すると　**clone元のリポジトリに push してしまう危険**がある


---

##  STEP 3：VSCode で.gitignore をまず定義する（重要）

ignore のタイミング＝　git addより前


---

##  STEP 4：git init

```
// 注：必ずプロジェクトのルート（最上位ディレクトリ）で実行する
git init
```
→　Git が  「今このフォルダにどんなファイルがあるか」を初期状態として認識

---

## STEP 5：git add . → 初回commit

```
git add .
git commit -m "first clean commit"
```

```php
// 初回 commit の時点でプロジェクト内に入っているもの
/app
/bootstrap
/config
/database
/docker
/public
/resources
/routes
/tests
composer.json
composer.lock
docker-compose.yml
package.json
.env.example
```

```php
// 入ってないもの
- data   
- vendor  
- node_modules 
- storage 
- MySQL の残骸
```

→　クリーンな状態でcommitできる
（ブランチ名変更やリモートリポジトリ紐づけはpushの段階で行う）

---

## STEP 6：git push

```
// 1 ブランチ名を「main」に変更(プロジェクトルートでls, gitがあるか確認)
git branch -M main

// 2 自分のリモートリポジトリを紐づけ  (リモートリポジトリがある前提）
git remote add origin https://github.com/EKasai/attendance_system.git    // ←例

// 3 重要：push先の確認(clone元ではなく、自分のリポジトリか？）
git remote -v 

// 3 git push
git push origin main
```

---

## STEP ７：環境構築（Docker 起動 / 依存パッケージ install / .env etc）
```
// docker 起動
docker compose up -d --build    // 古いキャッシュ防止の為、初回はbuildをつける
docker ps

// "PHPコンテナ内"で 依存パッケージをインストール（vendor生成）
docker compose exec php composer install

// "phpコンテナ内"で .env.exampleをコピーし.envファイル作成
docker compose exec php cp .env.example .env

```

→　　MySQLコンテナが起動しMySQLの初期化（/var/lib/mysql の構築）が行われる = `docker/mysql/data/` にMySQLデータが作られる
→　`vendor`が生成される

これより前に、すでに `.gitignore` を作成済みなので、Git はこれらを一切追跡しない = ごみファイルが混ざらない。

---

## STEP 8 : .env編集

```
// .env 以下変更

- DB_HOST=127.0.0.1
+ DB_HOST=mysql
  
- DB_DATABASE=laravel
- DB_USERNAME=root
- DB_PASSWORD=

+ DB_DATABASE=laravel_db
+ DB_USERNAME=laravel_user
+ DB_PASSWORD=laravel_pass
```

---

## 9. MySQL設定確認

[[MySQL 設定確認]]

---

## 10. phpMyAdmin で GUI 接続確認

http://localhost:8080/

GUI でログインできれば MySQL 側は問題なし。

---



[^1]: git clone にフォルダ名を指定すると、
	
	git clone <URL> attendance_system
	
	
	このコマンドはこういう動作になります：
	
	カレントディレクトリ（今いる場所）の中に
	attendance_system という新しいフォルダが作られる
	
	その中にリポジトリの内容が展開される
	
	つまり、あなたが今いる場所は変わりません。
	
	作られたフォルダはあくまで 中身がコピーされた“だけ”。
	
	🟣 図でイメージ
	
	今
	C:\work\
	にいるとして、

	git clone URL attendance_system
	
	をするとこうなる：


	C:\work\
	    ├─ attendance_system ← ここに中身が入る
	    └─ （移動はしてないのでまだ C:\work のまま）

	なので…
	次に作業を続けるには
	cd attendance_system
	が必要。
