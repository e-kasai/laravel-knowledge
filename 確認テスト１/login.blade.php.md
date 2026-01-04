
```html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FashionablyLate</title>
    <link rel="stylesheet" href="{{ asset('css/sanitize.css') }}" />
    <link rel="stylesheet" href="{{ asset('css/login.css') }}" />
</head>
<body>
    <header class="header">
        <div class="header__container">
            <h1 class="header__title">FashionablyLate</h1>
            <a class="header__link" href="/register">register</a>
        </div>
    </header>
    <main>
        <section class="login__wrapper">
            <div class="login__container">
                <div class="login-form__header">
                    <h2 class="login-form__header-title">Login</h2>
                </div>
                <div class="login__content">
                    <form class="login-form" action="/login" method="POST" novalidate>
                        @csrf
                        {{-- メールアドレス --}}
                        <div class="login-form__content-group">
                            <label class="login-form__label" for="email">メールアドレス</label>
                            <input class="login-form__input" type="email" id="email" name="email"
                                value="{{ old('email') }}" placeholder="例:test@example.com" />
                            @error('email')
                                <div class="login-form__error">
                                    {{ $message }}
                                </div>
                            @enderror
                        </div>
                        {{-- パスワード --}}
                        <div class="login-form__content-group">
                            <label class="login-form__label" for="password">パスワード</label>
                            <input class="login-form__input" type="password" id="password" name="password"
                                placeholder="例:coachtech1106" />
                            @error('password')
                                <div class="login-form__error">
                                    {{ $message }}
                                </div>
                            @enderror
                        </div>
                        <div class="login-form__button">
                            <button class="login-form__button--submit" type="submit">ログイン</button>
                        </div>
                    </form>
                </div>
            </div>
        </section>
    </main>
</body>
</html>

```