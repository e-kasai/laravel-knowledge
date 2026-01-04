```php

<?php

namespace App\Providers;

use App\Actions\Fortify\CreateNewUser;
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\ServiceProvider;
use Laravel\Fortify\Fortify;
use App\Http\Requests\UserRequest;
use Laravel\Fortify\Http\Requests\LoginRequest;
use App\Models\User;
use Illuminate\Support\Facades\Hash;
use App\Http\Requests\UserLoginRequest;

class FortifyServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        // Fortifyが使うLoginRequestを自作UserRequestに差し替える
        $this->app->bind(LoginRequest::class, UserLoginRequest::class);

        // Fortifyログインの認証ロジックを再定義
        Fortify::authenticateUsing(
            function (\App\Http\Requests\UserLoginRequest $request) {
                $user = \App\Models\User::where('email', $request->email)->first();

                if ($user && \Illuminate\Support\Facades\Hash::check($request->password, $user->password)) {
                    return $user;
                }

                // 認証失敗 → null返すとFortifyがエラー扱いにする
                return null;
            }
        );

        Fortify::createUsersUsing(CreateNewUser::class);

        Fortify::registerView(function () {
            return view('auth.register');
        });

        Fortify::loginView(function () {
            return view('auth.login');
        });

        RateLimiter::for('login', function (Request $request) {
            $email = (string) $request->email;
            return Limit::perMinute(10)->by($email . $request->ip());
        });
    }
}


```