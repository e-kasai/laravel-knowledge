
---

📦storage
 ┣ 📂app　　                  # 非公開のアプリデータを保存する領域（Web公開されない）　[^3]
 ┃ ┣ 📂public               # アップロード画像の保存場所　　[^4]
 ┃ ┃ ┣ 📂items           # 自分で作ったフォルダの一例
		 ┗ 📜.gitkeep
 ┃ ┃ ┣ 📂material_images　　　　# 自分で作ったフォルダの一例
		 ┗ 📜.gitkeep
 ┃ ┃ ┗ 📜.gitignore     [^2]   
 ┃ ┗ 📜.gitignore         [^1]

 ┣ 📂framework        # Laravel本体が使う一時ファイルやキャッシュを格納
 ┃ ┣ 📂testing         # PHPUnitなどのテスト実行時に使う一時ファイル
 ┃ ┃ ┣ 📂disks
 ┃ ┃ ┃ ┗ 📂public
 ┃ ┃ ┃ ┃ ┗ 📂items
 ┃ ┃ ┗ 📜.gitignore
 ┃ ┣ 📂views         # Bladeテンプレートのキャッシュ済みHTML
 ┃ ┃ ┗ 📜.gitignore
 ┃ ┣ 📂cache                # ルート・設定・クエリキャッシュなど
 ┃ ┃ ┣ 📂data              # キャッシュデータの格納先
 ┃ ┃ ┗ 📜.gitignore
 ┃ ┣ 📂sessions           # ファイルドライバ使用時のセッション情報
 ┃ ┃ ┗ 📜.gitignore
 ┃ ┗ 📜.gitignore
 
 ┗ 📂logs                     # アプリのログ（laravel.log など）
  ┃ ┣ 📜laravel.log      
  ┃ ┗ 📜.gitignore

[^1]: *
	!public/
	!.gitignore

[^2]: *
	!material_images/
	!items/
	!items/.gitkeep
	!material_images/.gitkeep
	!.gitignore

[^3]:  storage/app (非公開)
	* Laravelでファイルを保存するときのデフォルトがここ
	* 外部からURLで直接アクセスできない
	* アプリが内部的に使う「非公開の保存場所」
	* ここに置いたファイルは 公開したくない情報を守るのに使う
	
	例：
	- CSVの一時保存
	- バックアップデータ
	- Laravelの内部処理用ファイル
	

[^4]: storage/app/public（非公開→公開にできる）
	* アプリ実行中に保存されるファイル（ユーザーがアップロードするやつ）を置く場所。
		例：プロフィール画像、商品写真、投稿画像など。
	* デフォルトでは外から直接アクセスできない。
	`php artisan storage:link`を実行すると`public/storage` → `storage/app/public`のリンクができて、URLでアクセスできるようになる：
	```
	http://localhost/storage/uploads/item.png
	```
	* Git管理対象にはしない（ユーザーごとに変わるから）
