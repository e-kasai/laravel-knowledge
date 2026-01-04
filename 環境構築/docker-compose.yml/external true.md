
## external: true とは

**external: true** は  
「このネットワークは Docker Compose の管理外。すでに外にあるものを使うだけ」
という指定。

---

## 設定例

```yaml
networks:
  my-net:
    external: true
```

---

## external: true を付けた場合の挙動

### docker compose up
- ネットワークは **作られない**
- すでに存在しないと **エラーになる**

```text
network my-net declared as external, but could not be found
```

### docker compose down
- ネットワークは **削除されない**

---

## 事前に必要な作業

external を使う場合は、先にネットワークを作っておく必要がある。

```bash
docker network create my-net
```

---

## external を使う理由

- 複数の compose で同じネットワークを共有したい
- DB を複数アプリで使い回したい
- app を落としても DB は生かしたい
- docker compose down で消えると困る

「別スタックでDBを立てる」　＝　別の docker compose プロジェクトでDBを立てる

## 別スタックの具体例

### スタックA（アプリ側）

```text
app/
 └ docker-compose.yml
    - php
    - nginx
```

### スタックB（共通DB）

```text
db/
 └ docker-compose.yml
    - mysql
```


---

## external なしとの違い

| 項目 | external なし | external: true |
|---|---|---|
| ネットワーク作成 | Compose が作る | 作らない |
| down 時の削除 | 削除される | 削除されない |
| 管理者 | Docker Compose | Docker |
| 他 compose との共有 | 基本不可 | 可能 |

---

## 重要な整理

- **明示したかどうかは関係ない**
- **external: true かどうかが境界線**
- docker compose down で削除されるのは  
  **Compose が作ったネットワーク**

---

## 覚え方

- external: true = **「触るな宣言」**
- 共有したい / 消えたら困る → external
- その compose 専用 → external なし
