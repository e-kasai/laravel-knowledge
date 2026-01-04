```html

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>FashionablyLate</title>
    <link rel="stylesheet" href="{{ asset('css/sanitize.css') }}" />
    <link rel="stylesheet" href="{{ asset('css/register.css') }}" />
</head>
<body>
    <header class="header">
        <div class="header__container">
            <h1 class="header__title">FashionablyLate</h1>
            <a class="header__login" href="/login">login</a>
        </div>
    </header>
    <main>
        <section class="register__wrapper">
            <div class="register__container">
                <div class="register-form__header">
                    <h2 class="register-form__header-title">Register</h2>
                </div>
                <div class="register__content">
                    <form class="register-form" action="/register" method="POST" novalidate>
                        @csrf
                        <div class="register-form__content-group">
                            <label class="register-form__label" for="name">お名前</label>
                            <input class="register-form__input" type="text" id="name" name="name"
                                value="{{ old('name') }}" placeholder="例:山田 太郎" />
                            @error('name')
                                <div class="register-form__error">
                                    {{ $message }}
                                </div>
                            @enderror
                        </div>
                        <div class="register-form__content-group">
                            <label class="register-form__label" for="email">メールアドレス</label>
                            <input class="register-form__input" type="email" id="email" name="email"
                                value="{{ old('email') }}" placeholder="例:test@example.com" />
                            @error('email')
                                <div class="register-form__error">
                                    {{ $message }}
                                </div>
                            @enderror
                        </div>
                        <div class="register-form__content-group">
                            <label class="register-form__label" for="password">パスワード</label>
                            <input class="register-form__input" type="password" id="password" name="password"
                                placeholder="例:coachtech1106" />
                            @error('password')
                                <div class="register-form__error">
                                    {{ $message }}
                                </div>
                            @enderror
                        </div>
                        <div class="register-form__button">
                            <button class="register-form__button--submit">登録</button>
                        </div>
                    </form>
                </div>
            </div>
        </section>
    </main>
</body>
</html>


```