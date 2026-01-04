
---
#### 役割
Laravelの お助け関数
public フォルダに置いたファイルへの フルURL をパッと作ってくれる

---
#### 例1：cssファイル
```php
<link rel="stylesheet" href="{{ asset('css/style.css') }}">
```

---
#### 出力
```php
//ローカル環境の場合
http://localhost/css/style.css

//本番環境の場合
https://yourdomain.com/css/style.css
```

** public フォルダ直下を起点にしてくれる **

---
#### なぜpublic/css/style.cssじゃない？

プロジェクトフォルダには
`/project-root/public/css/style.css`　ってファイルがある。

- サーバー設定（ApacheやNginx）で「ドキュメントルート」を public/ にしてある

```php
//プロジェクトの実際のパス
/var/www/myapp/public/css/style.css

//サーバー設定（NginxとかApache）が設定してるDocumentRoot
/var/www/myapp/public
```

だからブラウザでアクセスするときは↓こう　(css/～でOK)
`http://localhost/css/style.css`

* `/var/www/myapp/public`は公開ルートだからわざわざURLには出ない

---

** publicを書いてしまうと２重になる **
```php
//publicを書くと
<link rel="stylesheet" href="{{ asset('public/css/style.css') }}">

//実際のURLはこうなってしまう(余計にpublicが入った状態)
http://localhost/public/css/style.css
```

---
#### 例２: 画像やストレージと組み合わせ

* アップロードした画像を `storage/app/public` に入れて`php artisan storage:link` で `public/storage` にリンクを張るのが定番。

```php
//public配下からのパスをassetに記載
<img src="{{ asset('storage/items/sneaker.jpg') }}">

//ユーザーから見えるURL
http://localhost/storage/items/sneaker.jpg
```


