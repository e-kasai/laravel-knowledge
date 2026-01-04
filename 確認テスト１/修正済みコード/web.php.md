```php

<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\ContactController;
use App\Http\Controllers\Auth\RegisterController;
use App\Http\Controllers\AdminController;

//修正:登録はFortifyのルートを使用（Fortifyの/registerはデフォルトでguestミドルウェアが付いている為）

// 管理画面
Route::middleware('auth')->group(function () { 
	Route::get('/admin', [AdminController::class, 'index']); 
	Route::delete('/admin/contacts/{id}', [AdminController::class, 'destroy']); 
	Route::post('/admin/export', [AdminController::class, 'export']); 
});


// お問い合わせフォーム表示
Route::get('/', [ContactController::class, 'index']);

// 確認画面表示
Route::post('/confirm', [ContactController::class, 'confirm']);


// DBにデータ保存とthanksページにリダイレクト
Route::post('/thanks', [ContactController::class, 'store']);


// リダイレクトされたらthanks画面表示
Route::get('/thanks', function () {
    return view('thanks');
});

// 修正: logoutもFortifyのルートを使用する為書かない


```