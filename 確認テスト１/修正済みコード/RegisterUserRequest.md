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
			//defaults()はApp\Providers\AuthServiceProviderで定義されているデフォルトのパスワードルール
			'password' => ['required', Password::defaults()],
        ];
    }

    public function messages():array
    {
        return [
            'name.required' => '名前を入力してください',
            'name.string' => '名前を文字列で入力してください',
            'name.max' => '名前を255文字以下で入力してください',
            'email.required' => 'メールアドレスを入力してください',
            'email.string' => 'メールアドレスを文字列で入力してください',
            'email.email' => '有効なメールアドレス形式を入力してください',
            'email.max' => 'メールアドレスを255文字以下で入力してください',
            'email.unique' => 'このメールアドレスは既に登録されています',
            'password.required' => 'パスワードを入力してください',
        ];
    }
}

```