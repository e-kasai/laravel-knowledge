---
tags: #DB作成 #relation #seed #migration #親テーブル #子テーブル
---

## 親子関係のあるテーブルを seed 付きで整備する流れ

### サンプルケース

```

categoriesテーブルが親（ダミーデータはseederファイルで作成)
contactsテーブルが子で親のcategory_idを参照する(ダミーデータはFactoryで作成)

```


### 成功するポイント

- DatabaseSeederの呼び出し順は親が先
- 外部キー制約付きの子テーブルをmigrationする前に、参照先の親テーブル自体が存在している
- migrationの実行順が親テーブルが先になるよう日時が変更済み
- 子テーブルをseedする前に、参照される親の行が存在している
- 未入力でもいい任意カラムには->nullable()がありエラー回避されている



#### 1 マイグレーション作成（構造定義）

1. categoriesテーブル(親側のテーブル)用マイグレーションを作る  
    実行コマンド：`php artisan make:migration create_categories_table`   
     
2. contactsテーブル(子側のテーブル)用マイグレーションを作る（外部キー定義を含めるのを忘れずに！）  
    実行コマンド：`php artisan make:migration create_contacts_table`

	```php
	
	// 外部キーの定義
	Schema::create('contacts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('category_id')
          ->constrained('categories') // テーブル名明示→categories.id を確実に参照
          ->cascadeOnDelete(); // 親が消えたら子も消す（必要に応じて調整）
    // 他のカラム...
    $table->timestamps();
});

	```


#### 2 マイグレーション実行（テーブルを物理的に作る）

- 両方のマイグレーションが揃っている状態で実行する (親テーブルのタイムスタンプが先であることを確認！)
    実行コマンド：`php artisan migrate`
    
   

#### 3 ファクトリ / シーダーの準備

1. Factory や seeder で"親テーブルのデータ"を先に用意
2. 子テーブルのデータを作成し外部キーを親テーブルから引っ張るロジックを記載する

例（親モデルの読み込み＆ランダムな外部キー取得）：
```php

use App\Models\Category;


'category_id' => Category::inRandomOrder()->value('id') ?? 1,

```


#### 4 シーダーの順番定義（`DatabaseSeeder` で親→子）

`DatabaseSeeder` の `run` を次のように：

```php

public function run()
{
    $this->call([
        CategoriesTableSeeder::class, // 親が先
        ContactsTableSeeder::class,   // 子はあと（ContactFactoryを内部で使う）
    ]);
}

```

- `ContactsTableSeeder` の中で `Contact::factory(35)->create();` のようにファクトリを呼び出す。


#### 5 シード実行

全体を実行する：  
```bash

`php artisan db:seed`

```

特定のシーダーだけ実行する例：  
```bash

`php artisan db:seed --class=CategoriesTableSeeder`  
`php artisan db:seed --class=ContactsTableSeeder`

```




