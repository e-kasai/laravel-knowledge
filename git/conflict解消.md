
---

## ステップ0：準備

以下のコマンドで練習用に安全な状態か確認してください。
`cd ~/project_files/git-lab/branch-visibility git status`

**✔️ブランチが `main` で、変更がないことを確認**してください。

---

## ステップ1：ファイルを作成＆コミット

まず、共通の編集対象ファイルを作ります。

```
echo "こんにちは from main" > hello.txt 
git add hello.txt 
git commit -m "add: hello.txt with main message"
```


---

## ステップ2：ブランチを切って編集

```
git switch -c feature/conflict-practice 
git add hello.txt 
git commit -m "edit: hello.txt on feature"
```
---

## ステップ3：main に戻って別の編集

```
git switch main 
echo "こんにちは from main branch (conflict)" > hello.txt 
git add hello.txt 
git commit -m "edit: hello.txt on main"
```

→ これで、**同じファイル・同じ行を両方で変更した状態**になる

---

## ステップ4：コンフリクト発生させる


```
git merge feature/conflict-practice
```

 **ここで「CONFLICT」って出たら成功**

---

## ステップ5：中身を確認しファイルを修正

ファイルを開くと、こんな状態になってる

`<<<<<<< HEAD こんにちは from main branch (conflict!) ======= こんにちは from feature branch >>>>>>> feature/conflict-practice`

`hello.txt` を修正
`from feature branchや <<<<< =======などは全て消す`

最終形イメージ

`こんにちは`

---

## ステップ6：解消を確定


```
git add hello.txt 
git commit -m "resolve: conflict in hello.txt"
```
