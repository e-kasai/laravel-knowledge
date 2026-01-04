---
tags:
  - "#fortify"
  - "#login"
  - "#auth"
  - "#FormRequest"
---
## 1. Login処理

### 分担
- validation = 自作FormRequest が担当
- ルート設定、login処理 = Fortify が担当


#### 自作FormRequest作成

例: `UserLoginRequest.php`

```php
<?php

namespace App\Http\Requests;
use Laravel\Fortify\Http\Requests\LoginRequest;

class UserLoginRequest extends LoginRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'email' => ['required', 'email'],
            'password' => ['required', 'string'],
        ];
    }

    public function messages()
    {
        return [
            'email.required' => 'メールアドレスを入力してください',
            'email.email' => 'メールアドレスは「ユーザー名＠ドメイン」形式で入力してください',
            'password.required' => 'パスワードを入力してください',
        ];
    }
}

```


---

#### FortifyServiceProvider設定

例: `FortifyServiceProvider.php`

```php

<?php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Laravel\Fortify\Fortify;
use Illuminate\Http\Request;

// fortifyの認証ロジック
use Laravel\Fortify\Http\Requests\LoginRequest;

//自作のFormRequest
use App\Http\Requests\UserLoginRequest;

use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Support\Facades\RateLimiter;  

class FortifyServiceProvider extends ServiceProvider

{
    public function register(): void
    {
        //
    }

    public function boot(): void
    {
         // 既定のルートを有効にする（※ ignoreRoutes() は使わない）
        // Fortify::ignoreRoutes();
  
        // Fortify が使う LoginRequest を自作のFR：UserLoginRequestに差し替える
        $this->app->bind(LoginRequest::class, UserLoginRequest::class);

          // login を開いたとき、auth/login.blade.php を表示する(route設定)
          //実際の認証（パスワード照合・セッション作成）は POST/loginをFortify側の既定ルートが受けた段階で自動で実行される
        Fortify::loginView(function () {
            return view('auth.login');
        });
 
        RateLimiter::for('login', function (Request $request) {
            $email = (string) $request->email;
            // 1分あたり5回
            return Limit::perMinute(5)->by($email . $request->ip());
        });
    }
}

```



---

#### 補足： 追加チェックを入れたいときの場所

`FortifyServiceProvider` の `boot` 内で：

たとえば「ユーザーが有効フラグを持っているか」などを入れるなら、`Fortify::authenticateUsing(...)` を 書く必要がある


##### 例：カスタム認証ロジック（有効フラグ付き）

Fortifyの既定認証に追加チェックを挟む。  
ここでは「メール・パスワード・`is_active` フラグ」の全部を通った（ログイン禁止や承認待ちでないユーザー）のときだけログインを許可

```php

namespace App\Http\Requests;

use Laravel\Fortify\Http\Requests\LoginRequest;
use Illuminate\Support\Facades\Hash;
use App\Models\User;

Fortify::authenticateUsing(function (LoginRequest $request) {
    $user = User::where('email', $request->email)->first();

    if (
        $user &&
        Hash::check($request->password, $user->password) &&
        $user->is_active) {
        return $user; // 認証成功
    }

    return null; // 認証失敗
});



```



