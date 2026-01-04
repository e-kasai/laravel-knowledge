---
tags:
---

### 説明
branchを切り替えて作業しているときに作業中のブランチにもmainにあるファイルなどを持ってきたいという時
branch間のファイル同期方法


# 1) 準備（新しい練習用リポジトリ）

実行場所：`project_files`

```
mkdir -p git-lab/branch-visibility && cd $_ git init
```

ファイル作成（READMEだけ）

```
printf "# Branch visibility lab\n" > README.md 
git add README.md 
git commit -m "init: README only"
```

 確認

```
git branch       # → * master または * main（どちらでもOK） 
ls               # → README.md だけ
```

---

# 2) 「あるブランチには存在しない」状態を作る

(1) **この段階でブランチを切る**（ここではまだ docker-compose.yml を作らない）  
実行場所：`~/git-lab/branch-visibility`

`git switch -c feature/a`

(2) **main に戻ってから** `docker-compose.yml` を追加

```
git switch main   # master の場合は master と書く 
cat > docker-compose.yml <<'YML' 
services:   
demo:     
image: alpine YML git add docker-compose.yml git commit -m "add: docker-compose.yml on main"`
```

(3) **feature/a に戻る**

```
git switch feature/a ls # ← README.md だけ。docker-compose.yml は「ない」→ これが“消えたように見える”の正体`
```

---

# 3) 方法①：ファイルだけ持ってくる（特定のファイルだけでいい時）

実行場所：`~/git-lab/branch-visibility`

```
git checkout main -- docker-compose.yml    # → feature/a に未ステージの docker-compose.ymlを上書きする
git add docker-compose.yml 
git commit -m "feat: bring docker-compose.yml from main"
```

✅ 確認

```
ls | grep docker-compose.yml 
```
---

# 4) 方法②：main の変更をマージする（全体を取り込み）

実行場所：`~/git-lab/branch-visibility`


```
# まず、ファイル持ってきたコミットを試しに取り消す（！これは練習用なのでgit reset --hardしてる） 
git reset --hard HEAD~1  # ここでファイルは消える（feature/aからは） 
ls | grep docker-compose.yml || echo "not found"  
# main の履歴ごと取り込む 
git merge main ls | grep docker-compose.yml 
```

> まとめ：
> 
> - ピンポイントに欲しい → **checkout/restore**
>     
> - Mainの更新を丸ごと反映 → **merge（または rebase）**
>     

---


## 要点の整理
 
- **戻す手段は2つ**：  
    ① `git checkout main -- ファイル`（or `git restore --source=main ファイル`）  
    ② `git merge main`（全体を取り込む）
    
---







