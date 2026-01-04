---
tags: #DataBaseSeederファイル #ファクトリ呼び出し
---

### 説明
defaultのseederファイルの中を解説

### code

```php

<?php

// このファイルが属する「住所」Laravelではシーダー類は `Database\Seeders` 名前空間に置かれる
namespace Database\Seeders;

// 親クラス `Seeder` をインポート →`Seeder` クラスをフルパス書かずに使えるようになる
use Illuminate\Database\Seeder;

// このシーダーをLaravel の `Seeder` 基底クラスを継承して動かす
class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        // この中でseedが先に必要な順に並べる（親が先）
        
        // 以下はデフォルトで記載のサンプルコード
        // ファクトリを使ってユーザーを10件作る例
        // \App\Models\User::factory(10)->create();

		// ファクトリでキーと値を指定してダミーデータを1件作る例
        // \App\Models\User::factory()->create([
        //     'name' => 'Test User',
        // ]);


		// 実際にシーダーやFactoryを呼ぶときの例↓
		
		// シーダーファイル呼び出し（単数）
		$this->call([
	    CategoriesTableSeeder::class,

		// シーダーファイル呼び出し（複数）
		$this->call([  
	    AuthorsTableSeeder::class,  
	    PostsTableSeeder::class,  
		]);

		// Factory呼び出し例
	    // Contactモデルを呼び出してるのでuseで呼び出し必要
	    // use App\Models\Contact;
	    Contact::factory(35)->create();
		]);
    }
}




```

