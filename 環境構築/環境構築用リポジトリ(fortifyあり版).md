---
tags: 
---

### 説明

- 通常版からコピーしてFortifyを導入したバージョン
- `composer require laravel/fortify` 済み
- `FortifyServiceProvider` 設定済み、認証画面あり


### step1　通常版の環境をclone
- 通常版からclone

```
cd project_files
git clone 通常版URL

mv laravel-template-base laravel-template-fortify
```

---
### step2  .gitディレクトリ削除

- エクスプローラから`.gitディレクトリ`を削除
※clone元の.gitがついてくるので別ディレクトリとして扱うためには一度.gitを削除してgit initが必要

---

### step3 Fortifyの整備


### step4 新しいリポジトリ作成
- リポジトリ名  `laravel-template-fortify`

---
### step5 作成したリポジトリにpush

```
cd laravel-template-fortify
git init
git branch -M main
git remote add origin git@github.com:<ユーザー名>/laravel-template-fortify.git
git add .
git commit -m "Initial commit for laravel-template-fortify"
git push -u origin main
```


