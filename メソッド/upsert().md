
---
tags: [laravel, upsert, seeder]
---

# upsert 

## 概要
- **INSERT と UPDATE を 1 クエリで両立**  
- UNIQUE キーが被る → UPDATEする
　UNIQUEキーが無い → INSERTする
- **Laravel 8.34 以上** で利用可（8.83.8 なら OK）

## 使いかた
```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Carbon\Carbon;

class CategoriesTableSeeder extends Seeder
{
    public function run(): void
    {
        //現在時刻を確定しseeder内で使いまわす = 時刻ずれ防止
        $now = Carbon::now();

        $rows = [
            ['name' => 'ファッション',   'created_at' => $now, 'updated_at' => $now],
            ['name' => '家電',          'created_at' => $now, 'updated_at' => $now],
            ['name' => 'インテリア',     'created_at' => $now, 'updated_at' => $now],
            ['name' => 'レディース',     'created_at' => $now, 'updated_at' => $now],
            ['name' => 'メンズ',        'created_at' => $now, 'updated_at' => $now],
            ['name' => 'コスメ',        'created_at' => $now, 'updated_at' => $now],
            ['name' => '本',            'created_at' => $now, 'updated_at' => $now],
            ['name' => 'ゲーム',        'created_at' => $now, 'updated_at' => $now],
            ['name' => 'スポーツ',      'created_at' => $now, 'updated_at' => $now],
            ['name' => 'キッチン',      'created_at' => $now, 'updated_at' => $now],
            ['name' => 'ハンドメイド',   'created_at' => $now, 'updated_at' => $now],
            ['name' => 'アクセサリー',   'created_at' => $now, 'updated_at' => $now],
            ['name' => 'おもちゃ',      'created_at' => $now, 'updated_at' => $now],
            ['name' => 'ベビー・キッズ', 'created_at' => $now, 'updated_at' => $now],
        ];

        // nameがすでにある場合はそのまま、ない場合はinsert
        DB::table('categories')->upsert(
            $rows,          // 挿入・更新対象行
            ['name'],       // 一意キー
            ['updated_at']  // 既存行があれば updated_at のみ更新
        );
    }
}

```

#### よくある用途
* マスターデータを 毎回リセットせず同期
* CSV など大量レコードの高速取り込み

 #### updateOrInsert との違い
|比較項目|upsert|updateOrInsert|
|---|---|---|
|   |   |   |
|対象行|複数行まとめて|1 行ずつ|
|   |   |   |
|SQL 発行回数|1 回|最大 2 回／行|
|   |   |   |
|同時実行安全性|◎（原子的）|△（レース注意）|

#### チェックリスト
 * php artisan --version が 8.34+

 * マイグレーションで UNIQUE 制約を設定

 * 10 万行超は chunk() で分割投入