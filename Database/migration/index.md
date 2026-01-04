
---

## インデックス（index）

データベースの検索を高速化するための仕組み。
テーブルの中の特定のカラムをもとに「検索用の地図（索引）」を作っておき、
データを探すスピードを上げる。

---

#### マイグレーション例
```php
Schema::create('attendances', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->cascadeOnDelete();
    $table->date('date');
    $table->timestamps();

    
    $table->unique(['user_id', 'date']);
    $table->index(['user_id', 'date']); // 検索高速化
});
```

#### インデックスが効く検索の例

// ユーザーごとの月別勤怠を取得
```php
Attendance::where('user_id', 5)
    ->whereBetween('date', ['2023-06-01', '2023-06-30'])
    ->get();

```
→ user_id × date のインデックスがあると、この検索が高速に実行される。

---

### 注意点

| 注意点 | 内容 |
|--------|------|
| ✅ 効果的に使う | WHERE句でよく使うカラムにだけ付ける |
| ⚠️ 付けすぎ注意 | 登録・更新時にインデックスも更新されるため、過剰につけると処理が遅くなる |
| 🧪 確認方法 | EXPLAIN コマンドでクエリの実行計画を確認できる |

---

### まとめ

| 項目 | 内容 |
|------|------|
| 目的 | 検索の高速化 |
| 作る場所 | マイグレーションで `$table->index()` |
| 表示 | テーブル上には出ない（内部構造） |
| よく使う組み合わせ | `$table->unique()` ＋ `$table->index()` |

---

### 補足

#### インデックスの働き方

このクエリの裏側では MySQL が
user_id → date の順に検索しています。

つまり：

user_id = 5 でまず候補を絞る

その中から date の範囲（2023-06-01〜2023-06-30）を探す

この「user_id→date」の組み合わせが
インデックスとして登録されていれば、
MySQLは最小限の範囲だけを読み取って済む、という仕組みです。

---

💡 もし whereMonth() で検索するとどうなる？

whereMonth() は内部的にこう変換されます：
where MONTH(date) = 6 and YEAR(date) = 2023

この形では関数が使われているため、単純なインデックスでは効かないことがあります。
そのため、月単位の検索を頻繁に行う場合は、別の方法（BETWEEN検索） にするのが最適です：

```php
Attendance::where('user_id', 5)
    ->whereBetween('date', ['2023-06-01', '2023-06-30'])
    ->get();
```

この方が確実にインデックスが効きます。