---
tags: #ファクトリ #factory #dummy #ダミーデータ
---

### 説明
Factoryファイルはダミーデータを作るときの標準パターンを定義するファイル


### code

```bash
// 注：ファクトリ名 = モデル名 + Factoryにしないとエラーになる
php artisan make:factory ContactFactory
```

---

```php

<?php
namespace Database\Factories;
use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Contact;
  

class ContactFactory extends Factory

{
	// protected $model →このファクトリが作成,加工する対象のEloquentモデルの定義
	// Contact::class →そのクラスの完全修飾クラス名（名前空間込みの文字列）を返す
	// たとえば `App\Models\Contact::class` は `"App\Models\Contact"` という文字列になる
    protected $model = Contact::class;
 

    public function definition():array
    {
	    // 'ja_JP' にすると名字・名前・住所などが日本版で（例：「佐藤」「東京都渋谷区」）出てくる
        $faker = \Faker\Factory::create('ja_JP');
 
		// 変数で任意の値を定義もできる
        $dummyDetails = [
            '配送日の確認方法が知りたいです',
            '返品期限はいつまでですか？',
            'サイズが大きいので交換したいのですが可能ですか？',
            '届いたのですが注文したものとは違う商品が届きました',
            '先ほどの注文と合わせて送料無料にしたいのですが可能ですか？',
        ];
  
        return [
        
        
	        // User関連
	        'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(), //emailカラムにunique制約がある場合
			'email_verified_at' => now(),                   //メール認証完了したとき入る日時
            'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // passwordの文字列をハッシュ化したもの
            'password' => bcrypt('secret123')               //こっちの書き方もOK（任意の文字列をハッシュ化する）
            'remember_token' => Str::random(10),            //ログイン状態を保持にチェックを入れた時のtoken
           
            // profile関連
            'last_name'  => $faker->lastName,
            'first_name' => $faker->firstName,
            'gender'     => $faker->randomElement([1, 2, 3]),              // 配列 `[1,2,3]` のうちランダムに1つ選ぶ
            'email'      => $faker->safeEmail(),                           // テスト向けの安全なメールアドレス
            'tel'        => '080' . $faker->randomNumber(8, true),         // `'080'` で始まり、そのあとに8桁の数字
            'created_at' => $faker->dateTimeBetween('-30 days', 'now'),    // 過去30日以内のランダムな日時を返す
            
            // 住所関連
            'postal_code' => $this->faker->postcode(),
            'address' => $this->faker->address(),
            'building' => $this->faker->secondaryAddress(), //secondaryAddressは建物名っぽい値を返す
            'address'    => $faker->prefecture . $faker->city . $faker->streetAddress,  // 県名＋市名＋丁目・番地をつなげた日本式の住所
            
            //Item 関連
            'seller_id' => User::factory(), //別にあるFactoryの方で作るときはこんな書き方（この場合はUserFactory）
            'item_name' => $this->faker->word,
            'price'     => $this->faker->numberBetween(500, 5000),
            'is_sold'   => false,
            'image_path' => 'noimage.png',
            'brand_name'     => $this->faker->company,
            'condition' => 1,
            'description' => $this->faker->sentence,
            'detail'     => $this->faker->randomElement($dummyDetails), //
			'category_id' => Category::inRandomOrder()->value('id') ?? 1, 	// ランダムにidを１件返す、idがnullなら１を返す
        ];
        
        
        public function unverified(): static    //未認証ユーザーを作りたいときにunverifiedを使う
    {
	        return $this->state(function (array $attributes) {        //->state() は「既存の状態を上書きする」メソッド。									
            return [
                'email_verified_at' => null,                      //ここでは email_verified_at を null にして未認証ユーザーにしてる
            ];
        });
    }
    }
}

```

---

#### ユニットテストでの使い方

```// 未認証ユーザー
$user = User::factory()->unverified()->create();]
```