
```php

<?php

  

use Illuminate\Support\Facades\Route;

use App\Http\Controllers\ContactController;

use App\Http\Controllers\AdminController;

use Illuminate\Http\Request;

use Illuminate\Support\Facades\Auth;

  
  

// 管理画面の表示と検索

Route::get('/admin', [AdminController::class, 'index'])->middleware('auth');

// 管理画面から削除

Route::delete('/admin/contacts/{id}', [AdminController::class, 'destroy'])->middleware('auth');

// 管理画面からCSVエクスポート

Route::post('/admin/export', [AdminController::class, 'export'])->middleware('auth');

  
  

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

  
  

// ログアウト

Route::post('/logout', function (Request $request) {

    Auth::logout();

    $request->session()->invalidate();

    $request->session()->regenerateToken();

    return redirect('/login');

})->name('logout');
```