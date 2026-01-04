```php

<?php

namespace App\Actions\Fortify;

use App\Models\User;
use Illuminate\Support\Facades\Hash;
use Laravel\Fortify\Contracts\CreatesNewUsers;
//use App\Http\Requests\UserRequest;


class CreateNewUser implements CreatesNewUsers
{
    public function create(array $input): User
    {

		//修正:以下は無効化（FormRequestとvalidation競合させない為)
        //$request = new UserRequest();
        //Validator::make($input, $request->rules(), $request->messages())->validate();

        return User::create([
            'name' => $input['name'],
            'email' => $input['email'],
            'password' => Hash::make($input['password']),
        ]);
    }
}

```