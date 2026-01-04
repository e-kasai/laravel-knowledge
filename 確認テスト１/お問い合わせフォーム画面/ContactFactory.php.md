```php

// Before

<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use App\Models\Contact;

class ContactFactory extends Factory
{
    protected $model = Contact::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        $faker = \Faker\Factory::create('ja_JP');
        $dummyDetails = [
            '配送日の確認方法が知りたいです',
            '返品期限はいつまでですか？',
            'サイズが大きいので交換したいのですが可能ですか？',
            '届いたのですが注文したものとは違う商品が届きました',
            '先ほどの注文と合わせて送料無料にしたいのですが可能ですか？',
        ];

        return [
            'category_id' => $faker->numberBetween(1, 5),
            'last_name'  => $faker->lastName,
            'first_name' => $faker->firstName,
            'gender'     => $faker->randomElement([1, 2, 3]),
            'email'      => $faker->safeEmail(),
            'tel'        => '080' . $faker->randomNumber(8, true),
            'address'    => $faker->prefecture . $faker->city . $faker->streetAddress,
            'detail'     => $faker->randomElement($dummyDetails),
        ];
    }
}




// After 
<?php

namespace Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
**use App\Models\Category;**
use App\Models\Contact;

class ContactFactory extends Factory
{
    protected $model = Contact::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
    public function definition()
    {
        $faker = \Faker\Factory::create('ja_JP');
        $dummyDetails = [
            '配送日の確認方法が知りたいです',
            '返品期限はいつまでですか？',
            'サイズが大きいので交換したいのですが可能ですか？',
            '届いたのですが注文したものとは違う商品が届きました',
            '先ほどの注文と合わせて送料無料にしたいのですが可能ですか？',
        ];

        return [
            **'category_id' => Category::inRandomOrder()->value('id') ?? 1,**
            'last_name'  => $faker->lastName,
            'first_name' => $faker->firstName,
            'gender'     => $faker->randomElement([1, 2, 3]),
            'email'      => $faker->safeEmail(),
            'tel'        => '080' . $faker->randomNumber(8, true),
            'address'    => $faker->prefecture . $faker->city . $faker->streetAddress,
            'detail'     => $faker->randomElement($dummyDetails),
        ];
    }
}
