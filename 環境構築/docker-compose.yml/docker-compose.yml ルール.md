
---

# docker-compose.yml の基本ルール

## ① 基本ルール

- **インデントはスペースのみ**
  - タブは使わない
  - スペース2つ or 4つ（どちらかに統一）
- **階層で意味が決まる**
  - インデントがズレると別の意味になる
- **大文字・小文字は区別される**
  - service 名は完全一致が必要
- **commentは#**
- **文字列はダブルクオートかくオートで囲む**
---

## ② 全体構造の型

```yml
services:
  サービス名:
    設定キー: 値

networks:
  ネットワーク名:

volumes:
  ボリューム名:
```
### 実際の例
```
services:
  mysql:                       //サービス名（必須）
    image: mysql:8.0   
    container_name: my-mysql   //container name（任意、書かなければ自動的に割り当てされる）
```

- services は必須
- networks / volumes は必要なときだけ書く

## ③ services のルール

```yaml
services:
  mysql:
    image: mysql:5.7
```

- `mysql` は service 名
- この名前は以下で使われる  
  - コンテナの識別  
  - ネットワーク内のホスト名  
  - depends_on  
  - WORDPRESS_DB_HOST など
  
- service 名はコピペで揃えるのが安全

---

## ④ depends_on のルール

```yaml
depends_on:
  - mysql     # mysqlコンテナ作成後にコンテナを作成
```

または

```yaml
depends_on:
  mysql:
    condition: service_started
```

- 書いた service 名は services に必ず存在する必要がある
- 存在しないと undefined service エラーになる

---

## ⑤ environment のルール

```yaml
environment:
  KEY: value
```

- 中身はただの文字列
- docker compose config では正しさはチェックされない
- 実行時にエラーになることがある（DB_HOST など）

---

## ⑥ ports のルール

```yaml
ports:
  - "8085:80"
```

- 左：ホスト側ポート  
- 右：コンテナ側ポート  
- ブラウザでアクセスするのは左側

---

## ⑦ volumes のルール

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

- 左：volume 名  
- 右：コンテナ内のパス  
- docker compose down では削除されない

---

## ⑧ networks のルール

- 何も書かなくても自動で1つ作られる
- 明示したいときだけ定義する

---

## ⑨ チェックの基本手順

### 構文チェック
```bash
docker compose config
```

### 起動
```bash
docker compose up -d
```

### 動かないとき
```bash
docker compose logs サービス名
```

