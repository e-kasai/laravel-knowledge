
---

# Composerとは何か

Composer は **PHP用のパッケージ管理ツール**。  
PHPで使う外部ライブラリやフレームワークをまとめて管理するための道具。

---

# なぜ Composer が必要なのか

## 1. Laravel = composerで集めたライブラリの集合体

Laravelは以下のような部品でできている。

- フレームワーク本体
- 多数の外部ライブラリ
- それぞれのバージョン制約

これを手作業で集めて揃えるのは現実的ではない。

→ **Composerが自動で全部そろえる**

---

## 2. 依存関係を自動で解決してくれる

例：
- Aライブラリは B v2 が必要
- B v2 は C v1.3 が必要

Composerは  
「どの組み合わせなら壊れないか」を計算して  正しいバージョンを入れてくれる。

---

## 3. 同じ環境を再現できる

`composer.json` に

- 使うライブラリ
- 許可するバージョン範囲

が書かれている。

そのため、
- 別PC
- 別コンテナ
- 別の人

でも `composer install` するだけで  
**同じ構成の環境を再現できる**。

---

# なぜ Composer が必要か

Laravel(フレームワーク）を使うから

---

# Laravel以外なら不要か？

## Composerが不要　＝　素のPHP

- PHPを1〜数ファイルで書く
- 標準関数だけ使い外部ライブラリは使用しない

例：
```php
<?php
echo "hello";
```

---

## Composerが必要

- フレームワークを使う（Laravel / Symfony など）
- 外部ライブラリを使う


# Composerの扱い方は2パターン

---

## パターン①：docker-compose.yml に書く

```
docker compose run composer install  //run →「作業用の箱を今だけ作る」
```

```yaml
services:
  composer:
    image: composer:2
    volumes:
      - ./src:/app
    working_dir: /app
```

### 特徴

- Composer専用コンテナを 「その都度」 作る→役目が終わると終了→次に使うときまた起動
- PHPコンテナを汚さない
- CI/CDでよく使われる
- 依存関係の責務が分かれ、構成がきれい


---

## パターン②：Dockerfile に書く

```
docker compose exec php composer install   // exec →「起動中のphpコンテナに入って作業する」
```

```dockerfile
RUN curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer
```

### 特徴

- Composerは **環境の一部**

- この Dockerfile から作られた PHPイメージを基に起動したPHPコンテナ内にcomposerコマンドが存在
- phpコンテナは 起動しっぱなしのためいつでもcomposerが使える
- Dockerfileが同じなら、別プロジェクトでも同じ扱い

