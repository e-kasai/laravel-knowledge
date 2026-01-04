---
tags: #seeder #id明示
---

### 説明

id明示が必要になるパターン
	１　他テーブルがそのidを直接参照する（外部キーで）
		例：`contacts` テーブルに `category_id` を入れるときに、「このシードで入れたカテゴリ 1 は必ず “商品のお届けについて” 」という場合など
	２　再現性を持たせたいとき（テスト用・他環境でも同じID を保証したい）

### code

```php

<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;

class CategoriesTableSeeder extends Seeder
{
    public function run(): void
    {
        $categories = [
            ['id' => 1, 'content' => '商品のお届けについて'],
            ['id' => 2, 'content' => '商品の交換について'],
            ['id' => 3, 'content' => '商品トラブル'],
            ['id' => 4, 'content' => 'ショップへのお問い合わせ'],
            ['id' => 5, 'content' => 'その他'],
        ];
        DB::table('categories')->insert($categories);
    }
}
