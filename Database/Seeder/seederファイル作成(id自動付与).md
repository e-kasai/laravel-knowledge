---
tags: #seeder #自動連番 #DBファサード
---
```php

<?php

namespace Database\Seeders;
// 親クラス `Seeder` をインポート →`Seeder` クラスをフルパス書かずに使えるようになる
use Illuminate\Database\Seeder;
// DB::table()を使ってdata挿入してるのでDBファサードが必要
use Illuminate\Support\Facades\DB;

class CategoriesTableSeeder extends Seeder
{
    public function run(): void
    {
        $categories = [
            ['content' => '商品のお届けについて'],
            ['content' => '商品の交換について'],
            ['content' => '商品トラブル'],
            ['content' => 'ショップへのお問い合わせ'],
            ['content' => 'その他'],
        ];
        DB::table('categories')->insert($categories);
    }
}

```

