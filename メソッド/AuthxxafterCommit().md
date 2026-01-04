

`Auth::afterCommit()`

---

`afterCommit` は **DBファサード**（`Illuminate\Support\Facades\DB`）のメソッドです。  
要するに「**トランザクションがコミットされた後に実行する処理を登録**」します。

### ざっくり仕様

- 使い方：`DB::afterCommit(function () { /* 後で実行 */ });`
    
- **コミット成功時のみ**実行。ロールバック時は実行されない。
    
- トランザクション外で呼ぶと **即時実行**（登録→すぐ実行）になる。
```php
// トランザクション外で afterCommit を呼ぶ
// `afterCommit()` 呼び出し時点では トランザクションが存在しない → logger('A: ここは即時実行される') が すぐ実行される
DB::afterCommit(function () {
    logger('A: ここは即時実行される');
});

// その後、別のトランザクションを開始
DB::transaction(function () {
    // 何かDB更新処理
    logger('B: これはトランザクション内');
});

```
    
- ネストしていても **最外層のコミット後**に実行される。
```php
// 1234が全部終わってからafterCommitは実行される
// 1と２の後じゃない
DB::transaction(function () {
    logger('1: outer start');

    DB::transaction(function () {
        logger('2: inner start');

        DB::afterCommit(function () {
            logger('A: after commit fired');
        });

        logger('3: inner end');
    });

    logger('4: outer end');
});
```
    

### 使いどころ

- DB更新が確定してからやりたいI/O（例：**古い画像の削除**、外部API通知）に最適。

---

### 注意点、よくある間違い

#### トランザクションと連動できていない

「必ずコミット後に処理したい」場合は、**必ず `transaction` 内部で呼び出す**必要があります。

```php
DB::transaction(function () {
     // DB更新     
     logger('DB更新');    
     
	//transaction内で呼び出す！  
     DB::afterCommit(function () {         
     logger('コミット後に実行');     
     }); 
 });
```
このように書けば、「コミット完了後」に確実に処理が走ります。
