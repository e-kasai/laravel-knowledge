
---

# 前提：やってはいけないこと

### 1. git push -f コマンドの実行→ほかの人の作業が消える

「どのブランチで作業したか」ではなく
「どのブランチで git add / git commit したか」

これがすべて


##  状況パターン
```
main（本来触らない）
 └ app.blade.php を編集してしまった
    ・未コミットの場合（未add or add済）
    ・コミットしてしまった場合
    ・pushまでしてしまった場合
```
の 3パターン に分かれる。

---

# ① コミットしてない場合　(add済 / 未add 両方含む）＝　ブランチ切って移動でOK


```
git switch -c feature/fix-mistake  //新しい featureブランチへ避難させる

git switch main   //main に戻り、汚れが残ってないか確認 （もし変更が残っているなら、stash するか discard）
git status
```
→ switch -c で、未コミット変更を新しいブランチに持ち越すため、作業内容は新しいブランチに移動する。

※なぜブランチ切って移動するだけでOK？
**gitは、コミットしていない限り、どのブランチにも属していないと判断して変更をそのままブランチに持ち越すから**

---

# ② commit してしまった場合　＝　報告する

もし、許可がでればコミットを新branchに移動＆mainをリモートと同じに戻す

```bash
git switch -c feature/fix-mistake   # コミットを新ブランチに移動
git switch main
git status                          # 未コミットが残ってないか念のため確認（残ってればmainから退避させる）
git reset --hard origin/main        # mainの状態をリモートと同じ状態に戻す
```

→ これで ローカルmain 上の「誤ったコミット」は消える。
注：reset --hardする前に未コミット変更を別branchに退避させとかないと消える
　　　上の例では最初にそれを行っているのでOK

---

# ③ push までしてしまった場合　= 報告する（コマンドを絶対に実行しない）

1. commitまでしてしまった場合と同様に
```bash
git switch -c feature/fix-mistake   # 間違えたコミットを非mainに逃がす
git switch main
git status                          # 未コミットが残ってないか念のため確認（残ってればmainから退避させる）
git reset --hard origin/main        # main の状態をリモートと同じ状態に戻す
```

# 注：これより下は危険コマンドの為絶対に実行しない！！

2. 自分のローカルの状態にリモートのmainを上書きする
```bash
git push -f origin main     # GitHub の main も自分のローカルの状態に“合わせて書き換える”危険コマンド
```
※なぜこれをするか？
ローカル側は「事故をなかったことにした歴史」になっていて、GitHub 側は「事故を含んだ歴史」のままだから。
歴史が違うので、普通の push（追加） だと拒否される。
→自分のローカル状態をgit hub mainに上書き（-f）する

## このコマンドで起きる事故

- 他の人の作業が消える
- 他の人がpullできなくなりコンフリクトが大量に発生する


### 絶対に守られるルール：

#### main に force push は基本禁止

どうしても必要なら

チーム全体に事前共有
実行する時間を決める
その間メンバーは main を触らない




