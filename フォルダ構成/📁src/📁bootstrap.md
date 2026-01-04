
---

📦bootstrap
 ┣ 📂cache    
Laravelが起動時に「設定ファイルをキャッシュしたり、ルートをキャッシュしたり」する場所。
php artisan config:cache や route:cache を実行したときに使われる。
「アプリが自動でファイルを書き込む」ので、書き込み権限が必要。
 ┃ ┣ 📜services.php
 ┃ ┣ 📜packages.php
 ┃ ┗ 📜.gitignore
 ┗ 📜app.php