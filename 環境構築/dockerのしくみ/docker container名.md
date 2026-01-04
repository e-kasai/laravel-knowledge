
---

## Dockerコンテナ名の仕組み

- Docker Compose で起動するとき、基本は<プロジェクト名>-<サービス名>-<番号>っていう形になる

ex：

- プロジェクト名 → flea_market
- サービス名 → mysql
- 複数立ち上げた時の番号 → 1

だから フルの名前は `flea_market-mysql-1`
-1 はただの連番で、同じサービスを複数立てない限りずっと 1 のまま。

---
#### 実際に使うとき

docker ps で出てくる名前は 最後までで1つのコンテナ名。

だから exec するときは
```bash
docker exec -it flea_market-mysql-1 bash
```

みたいに連番まで含めて打つ必要がある

---
#### 小技

もし「番号が変わるのが嫌」って思うなら、docker-compose.yml に

`container_name: flea_mysql`
とか任意で決めた名前を書いておくと、常にその名前で固定できる