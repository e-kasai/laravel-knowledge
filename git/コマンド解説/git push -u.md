
---

`git push -u origin main` の `-u` は **upstream（アップストリーム）設定** の略

  一度 push したブランチを「デフォルトの送信先」に登録できる

---

##  `-u` の有り無しでどう変わる？

###  `-u` あり
```
git push -u origin main
```

次回からはこれだけでOK：
```
git push
```

Git が送り先（upstream）を覚えてくれる。

---

###  `-u` なし
```
git push origin main
```

次回も毎回書かないといけない：
```
git push origin main
```

Git に「送り先を覚えさせていない」状態。

---

## 他のブランチでも同じように使える

  ```
  git push -u origin feature/xxx
  ```
  とすれば、次回以降は  
  
  ```
  git push
  ```  
  だけで済む  

