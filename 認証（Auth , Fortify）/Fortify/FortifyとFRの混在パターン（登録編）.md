---
tags:
  - "#fortify"
  - "#CreateNewUser"
---
## 1. 登録処理

### 分担
- validation/ルート設定= 自分で設定
- 登録処理  = Fortify が担当

---

#### 自作FormRequest作成

 `RegisterUserRequest.php`

```php
<?php

namespace App\Http\Requests;
use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;
// Password::defaults() を使うために、`Password`クラスの完全修飾名（FQCN）を知らせる
use Illuminate\Validation\Rules\Password;

class RegisterUserRequest extends FormRequest
{
  
    public function authorize():bool
    {
        return true;
    }


    public function rules():array
    {
        return [
	        'name' => ['required', 'string', 'max:255'],
			'email' => ['required', 'string', 'email', 'max:255', Rule::unique('users', 'email')],
			//defaults()はApp\Providers\AppServiceProvider.phpで定義されているデフォルトのパスワードルール
			'password' => ['required', 'confirmed', Password::defaults()],
        ];
    }

    public function messages():array
    {
        return [
            'name.required' => 'お名前を入力してください',
            'name.string' => 'お名前を文字列で入力してください',
            'name.max' => 'お名前を255文字以下で入力してください',
            'email.required' => 'メールアドレスを入力してください',
            'email.string' => 'メールアドレスを文字列で入力してください',
            'email.email' => '有効なメールアドレス形式を入力してください',
            'email.max' => 'メールアドレスを255文字以下で入力してください',
            'email.unique' => 'このメールアドレスは既に登録されています',
            'password.required' => 'パスワードを入力してください',
            'password.confirmed' => 'パスワードと一致しません'
        ];
    }
}

```

#### Fortifyのvalidationを無効にしておく

`CreateNewUser`内でValidator部分を削除(RFと２重validationになるので)

```
<?php

namespace App\Actions\Fortify;
use App\Models\User;
use Illuminate\Support\Facades\Hash;
// use Illuminate\Support\Facades\Validator;
// use Illuminate\Validation\Rule;
use Laravel\Fortify\Contracts\CreatesNewUsers;

class CreateNewUser implements CreatesNewUsers
{
	//validation = FRなのでコメントアウト
    // use PasswordValidationRules;

    /**
     * Validate and create a newly registered user.
     *
     * @param  array<string, string>  $input
     */
    public function create(array $input): User
    {
        // Validator::make($input, [
        //     'name' => ['required', 'string', 'max:255'],
        //     'email' => [
        //         'required',
        //         'string',
        //         'email',
        //         'max:255',
        //         Rule::unique(User::class),
        //     ],
        //     'password' => $this->passwordRules(),
        // ])->validate();

        return User::create([
            'name' => $input['name'],
            'email' => $input['email'],
            'password' => Hash::make($input['password']),
        ]);
    }
}

```

---

#### ルート設定（web.phpのルート使用）

自分でルート設定　 `web.php`

```php

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ContactController;
use App\Http\Controllers\Auth\RegisterController;
use App\Http\Controllers\AdminController;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

//ユーザー（管理者）登録
//ログイン済みユーザーは登録ページにアクセスできないようにmiddleware('guest')を追加
Route::middleware('guest')->group(function(){
	Route::get('/register', [RegisterController::class, 'show'])->name('register.show');
	Route::post('/register', [RegisterController::class, 'store'])->name('register');
});

//以下略

```


Fortifyの登録ルートを無効化する `config/fortify.php`

```
// こうすると/register（Fortify標準）が作られなくなる＝自作ルートと競合しない。
 'features' => [
        // Features::registration(),
```

---

#### 登録処理（自前controller内でfortify既存のCreateNewUserを使用)

例: `RegisterController.php`

```php

<?php

// どこのグループか名前空間の定義
namespace App\Http\Controllers\Auth;
// 親コントローラの読み込み（共通クラスを継承して使うため)
use App\Http\Controllers\Controller;
// 自作フォームリクエスト
use App\Http\Requests\RegisterUserRequest;
// fortifyのユーザー登録の仕組み
use App\Actions\Fortify\CreateNewUser;
// RouteServiceProviderで定義されている"HOME"を使う為
use App\Providers\RouteServiceProvider;


class RegisterController extends Controller
{
    //登録画面の表示
    public function show()
    {
        return view('auth.register');
    }
	// ユーザー登録アクション
    // validationは自作FRを使い、登録ロジックは Fortify の CreateNewUser を再利用
    public function store(RegisterUserRequest $request, CreateNewUser $creator)
    {
        // $input = validation通過済み
        $input = $request->validated();

        // FortifyのcreateUsersUsing（既定）でユーザー作成
        $user = $creator->create($input);

        // ログイン
        auth()->login($user);
        //セッション再生成、セッション固定攻撃やなりすましを防ぐ
        $request->session()->regenerate();

        // RouteServiceProviderで定義済みのHOMEにリダイレクト
        return redirect()->intended(RouteServiceProvider::HOME);
    }
}


```

Provider から二重指定を消す  `App\Providers\FortifyServiceProvider::boot()`

```
// Fortify::createUsersUsing(CreateNewUser::class); // ← これ消す（重複の原因）
```
自作コントローラーで CreateNewUser を明示的に呼んでいる ➡ Fortify が自動で呼ぶ必要がない。

---

#### 補足1： Fortify の自動ルートを止めたい場合（例: ルートもコントローラーも処理も全部自作したい）

`FortifyServiceProvider` の `boot` 内で：

```php

use Laravel\Fortify\Fortify;

public function boot(): void {     
Fortify::ignoreRoutes();
}

### 説明
全自動ルートを無効化するので登録もログインも全部自分でルート設定、コントローラー設定が必要

```


#### 補足２：Password::defaults()のエラー文だけ変えたい

- 方法１：Password::defaults()を使わず自分でFRに定義（※そのフォームだけにルール適用）
```
use Illuminate\Validation\Rules\Password;

public function rules(): array {
return [ 
	'password' => [ 
	'required', 
	'confirmed', 
	// 8文字以上かつ大文字小文字、数字、記号をすべて含む
	Password::min(8)->mixedCase()->numbers()->symbols(),
	 ],
  ]; 
}

public function messages()
{
	// 例
    return [
        'password.mixed' => '大文字と小文字を必ず入れてください！',
        'password.numbers' => '数字も入れてください！',
    ];
}
```

- 方法２：FRはPassword::defaults()つかったままで、メッセージだけ`resources/lang/ja/validation.php`　でエラー文を再定義
（※`Password::defaults()`　はアプリ全体に適用される）

---
#### 補足３：Password::defaults()とは

- `vendor/laravel/framework/src/Illuminate/Validation/Rules/Password.php` に定義されている
- 実体は `Illuminate\Validation\Rules\Password` クラスにある
- アプリ全体に適用される＞FRは個別のフォームに適用　→　フォームごとに変えたいときはPasword::defaults()使わずFRで直に書けばOK（補足２の方法１）

例：passwordのvalidation ruleを定義したいとき（defaultのままだと"８文字以上"しか定義されない）

`AppServiceProvider` (呼び出し側)　で定義　
注： 定義元ファイル`framework/src/Illuminate/Validation/Rules/Password.php`　は触らない

```
// AppServiceProvider.php
use Illuminate\Validation\Rules\Password;

public function boot(): void
{
    Password::defaults(function () {
        return Password::min(8)
	        //大文字小文字混在
            ->mixedCase()
            ->numbers()
            ->symbols();
    });
}

```

注：`AppServiceProvider` を書いても、ルール側で `Password::defaults()` を使っていないと全体設定が反映されていないように見えるので
　　　必ずフォームの `rules()` に `Password::defaults()` を置くのを忘れない

---
#### 補足4: event の発火

- 登録後に認証メールを送信などのイベントがある場合
- Fortifyの登録ルートを無効化するを行っているため自分でイベント発火が必要
（Laravelの仕組みは Userモデルが MustVerifyEmail 実装 ＆ Registered イベント発火 → リスナーが認証メール送信）

```php
//App\Http\Controllers\Auth\RegisterController@store
use Illuminate\Auth\Events\Registered; // ← 追加

public function store(RegisterUserRequest $request, CreateNewUser $creator)
{
    $input = $request->validated();
    $user = $creator->create($input);

    // ★これを必ず呼ぶ：ここで「認証メール送信」の起点になる
    event(new Registered($user));

    auth()->login($user);
    $request->session()->regenerate();
    return redirect()->intended(\App\Providers\RouteServiceProvider::HOME);
}
```

補足：順番は event(new Registered($user)) → auth()->login($user) のどちらでもOKですが、
公式の流儀に合わせて上の順をおすすめ

---


