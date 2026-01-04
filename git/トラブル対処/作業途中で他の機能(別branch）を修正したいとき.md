

## 前提
- 今は `feature/chat` ブランチで作業中  
- まだ完成していない  
- 突然「取引画面のコントローラー修正して！」など、別作業が必要になった

---

# 状況別に使うべき対応


## ① コードが動く状態なら「WIPコミット」で避難する
途中でもとりあえず動く、壊れていない状態ならこれ。

### 手順
```bash
git add .
git commit -m "WIP: チャット機能作業途中"
git switch feature/fix-transaction
```
戻すときは：
```php
git switch feature/chat   #元のブランチにもどるだけでOK
```

### ポイント
- WIP（作業中）として雑にコミットしてOK  
- main にマージしなければ誰にも迷惑はかからない  

---

## ② コードが壊れていてコミットしたくない → stash を使う
途中すぎて動かない、エラーが出る状態なら stash が安全。

### 手順
```bash
git stash
git switch feature/fix-transaction
```

戻すときは：

```bash
git switch feature/chat
git stash pop
```

### ポイント
- 完全に「変更ゼロ」の状態にできる  
- ブランチ切り替えが確実に安全  
- 戻したときに元の編集がそのまま復活する

---

## ③ 長時間中断しそうなら WIP のまま push しておく
安全性を最大にしたい場合や、後で他のPCで作業したい時。

### 手順
```bash
git add .
git commit -m "WIP: Chat halfway"
git push origin feature/chat
git switch feature/fix-transaction
```

### ポイント
- remote にバックアップされるので消えない  
- 途中でも push は問題ない（main に載せなければOK）

---

# 状況別の最適解まとめ表

| 状況 | 最適手段 |
|------|----------|
| 途中だが動く状態 | WIPコミット |
| 壊れていてコミットしたくない | stash |
| 作業が長く中断しそう / 消したくない | WIPコミット＋push |

---


# 実務でのフロー（例）

1. `feature/chat` で作業中  
2. 修正依頼が来た  
3. 状況を見て WIP or stash  
4. 依頼された修正用のブランチを切る  
5. 修正  
6. `feature/chat` に戻って続き


