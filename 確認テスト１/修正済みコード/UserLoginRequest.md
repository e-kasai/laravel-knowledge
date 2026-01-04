```php
<?php

namespace App\Http\Requests;

// Fortify純正を継承
use Laravel\Fortify\Http\Requests\LoginRequest;


class UserLoginRequest extends LoginRequest
{

    public function authorize():bool
    {
        return true;
    }


    public function rules():array
    {
        return [
            'email' => ['required', 'email'],
            'password' => ['required'],
        ];
    }

    // エラー文を定義
    public function messages():array
    {
        return [
            'email.required' => 'メールアドレスを入力してください',
            'email.email' => 'メールアドレスは「ユーザー名＠ドメイン」形式で入力してください',
            'password.required' => 'パスワードを入力してください',
        ];
    }
}
```