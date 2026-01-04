
---
tags: [php, arrow-function, クロージャ, 学習メモ]
aliases: [fn, アロー関数, 無名関数]
---

# PHP アロー関数 vs 旧クロージャ 

## 1. 比較
| 比較項目 | **アロー関数 `fn`** | **旧クロージャ `function`** |
| --- | --- | --- |
| 導入バージョン | PHP 7.4〜 | PHP 5.3〜 |
| 記述量 | **短い**（1式のみ） | 長い（複数文 OK） |
| 戻り値 | **暗黙**（右側の式がそのまま返る） | `return` が必要 |
| 外部変数 | **自動キャプチャ** | `use ($変数名)` が必須 |
| 複数行処理 | ✖️（不可）配列1個＝1式はOK | ✔️（可能） |
| 主な用途 | map/filter など簡単変換 | 複雑ロジック・処理分岐 |

---

## 2. 基本構文

アロー関数
```php
$callback = fn ($x) => $x * 2;
fn キーワード
```
=> の右側が 戻り値（1式だけ）

旧クロージャ
```php
$callback = function ($x) {
    return $x * 2;
};
```

---

### 外部変数の扱い
アロー関数（自動コピー）
```php
$factor = 3;
$mul    = fn ($x) => $x * $factor;   // use 不要
```

旧クロージャ（use 指定）
```php
$factor = 3;
$mul    = function ($x) use ($factor) {
    return $x * $factor;
};
```
補足：アロー関数は「値をコピー」して保持。参照させたい場合は use (&$var) を使う or プロパティに格納する。


---

### 向いている場面
* map / filter / reduce など短い式	      = アロー関数
* 複数行のバリデーション・try‒catch など	  = 旧クロージャ

---

### 使用例（コレクション変換）

```php
// アロー関数版（短い）
$rows = $ids->map(fn ($id) => 
	['id' => $id, 'created_at' => $now]);
```

```php
// 旧クロージャ版（長いが柔軟）
$rows = $ids->map(function ($id) use ($now) {
    // ログ出力など追加処理も書ける
    return ['id' => $id, 'created_at' => $now];
});
```



