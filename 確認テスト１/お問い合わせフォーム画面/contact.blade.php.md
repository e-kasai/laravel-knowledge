```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FasionablyLate</title>
    <link rel="stylesheet" href="{{ asset('css/sanitize.css') }}" />
    <link rel="stylesheet" href="{{ asset('css/contact.css') }}" />
</head>
<body>
    <header class="header">
        <div class="header__container">
            <h1 class="header__title">FashionablyLate</h1>
        </div>
    </header>
    <main>
        <section class="contact__wrapper">
            <div class="contact__container">
                <div class="contact-form__header">
                    <h2 class="contact-form__header-title">Contact</h2>
                </div>
                <div class="contact__content">
                    <form class="contact-form" action="/confirm" method="POST" novalidate>
                        @csrf
                        {{-- 名前 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">お名前</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    // 名前は姓名でそれぞれ分けてinput欄を設ける
                                    <input class="form__input--last" type="text" name="last_name" placeholder="例:山田"
                                        value="{{ old('last_name', $contact['last_name'] ?? '') }}" required />
                                    <input class="form__input--first" type="text" name="first_name"
                                        placeholder="例:太郎" value="{{ old('first_name', $contact['first_name'] ?? '') }}"
                                        required />
                                </div>
                                @if ($errors->has('last_name'))
                                    <div class="contact-form__error">
                                        {{ $errors->first('last_name') }}
                                    </div>
                                @elseif($errors->has('first_name'))
                                    <div class="contact-form__error">
                                        {{ $errors->first('first_name') }}
                                    </div>
                                @endif
                            </div>
                        </div>
                        {{-- 性別 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">性別</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    {{-- 男性 --}}
                                    <div class="form__gender-option">
                                        <input class="form__input--gender" id="male" type="radio" name="gender"
                                            value="1"
                                            {{ old('gender', $contact['gender'] ?? '') == 1 ? 'checked' : '' }}
                                            checked="checked" required>
                                        <label class="form__label" for="male">男性</label>
                                    </div>
                                    {{-- 女性 --}}
                                    <div class="form__gender-option">
                                        <input class="form__input--gender" id="female" type="radio" name="gender"
                                            value="2"
                                            {{ old('gender', $contact['gender'] ?? '') == 2 ? 'checked' : '' }}
                                            required>
                                        <label class="form__label" for="female">女性</label>
                                    </div>
                                    {{-- その他 --}}
                                    <div class="form__gender-option">
                                        <input class="form__input--gender" id="other" type="radio" name="gender"
                                            value="3"
                                            {{ old('gender', $contact['gender'] ?? '') == 3 ? 'checked' : '' }}
                                            required>
                                        <label class="form__label" for="other">その他</label>
                                    </div>
                                </div>
                                @error('gender')
                                    <div class="contact-form__error">
                                        {{ $message }}
                                    </div>
                                @enderror
                            </div>
                        </div>
                        {{-- メールアドレス --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">メールアドレス</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    <input class="form__input" type="email" name="email"
                                        placeholder="例:test@example.com"
                                        value="{{ old('email', $contact['email'] ?? '') }}" required />
                                </div>
                                @error('email')
                                    <div class="contact-form__error">
                                        {{ $message }}
                                    </div>
                                @enderror
                            </div>
                        </div>
                        {{-- 電話番号 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">電話番号</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    <input class="form__input" type="tel" name="tel1" placeholder="080"
                                        value="{{ old('tel1', $contact['tel1'] ?? '') }}" required /><span
                                        class="hyphen">-</span>
                                    <input class="form__input" type="tel" name="tel2" placeholder="1234"
                                        value="{{ old('tel2', $contact['tel2'] ?? '') }}" required /> <span
                                        class="hyphen">-</span>
                                    <input class="form__input" type="tel" name="tel3" placeholder="5678"
                                        value="{{ old('tel3', $contact['tel3'] ?? '') }}" required />
                                </div>
                                @if ($errors->has('tel1'))
                                    <div class="contact-form__error">
                                        {{ $errors->first('tel1') }}
                                    </div>
                                @elseif($errors->has('tel2'))
                                    <div class="contact-form__error">
                                        {{ $errors->first('tel2') }}
                                    </div>
                                @elseif($errors->has('tel3'))
                                    <div class="contact-form__error">
                                        {{ $errors->first('tel3') }}
                                    </div>
                                @endif
                            </div>
                        </div>
                        {{-- 住所 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">住所</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    <input class="form__input" type="text" name="address"
                                        placeholder="例:東京都渋谷区千駄ヶ谷1-2-3"
                                        value="{{ old('address', $contact['address'] ?? '') }}" required />
                                </div>
                                @error('address')
                                    <div class="contact-form__error">
                                        {{ $message }}
                                    </div>
                                @enderror
                            </div>
                        </div>
                        {{-- 建物名 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">建物名</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    <input class="form__input" type="text" name="building"
                                        placeholder="例:千駄ヶ谷マンション101"
                                        value="{{ old('building', $contact['building'] ?? '') }}" />
                                </div>
                            </div>
                        </div>
                        {{-- お問い合わせの種類 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">お問い合わせの種類</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <div class="form__input-group">
                                    <select class="form__input--category" name="category_id" required>
                                        <option value="" selected disabled>選択してください</option>
                                        @foreach ($categories as $category)
                                            <option value="{{ $category->id }}"
                                                {{ old('category_id', $contact['category_id'] ?? '') == $category->id ? 'selected' : '' }}>
                                                {{ $category->content }}
                                            </option>
                                        @endforeach
                                    </select>
                                </div>
                                @error('category_id')
                                    <div class="contact-form__error">
                                        {{ $message }}
                                    </div>
                                @enderror
                            </div>
                        </div>
                        {{-- お問い合わせ内容 --}}
                        <div class="contact-form__content-group">
                            <div class="contact-form__label">
                                <span class="form__label--item">お問い合わせ内容</span>
                                <span class="form__label--required">※</span>
                            </div>
                            <div class="contact-form__input">
                                <textarea class="form__input--detail" name="detail" rows="6" cols="30"
                                    placeholder="お問い合わせ内容をご記載ください" required>{{ old('detail', $contact['detail'] ?? '') }}</textarea>
                                @error('detail')
                                    <div class="contact-form__error">
                                        {{ $message }}
                                    </div>
                                @enderror
                            </div>
                        </div>
                        <div class="contact-form__button">
                            <button class="contact-form__button--submit" type="submit">確認画面</button>
                        </div>
                    </form>
                </div>
            </div>
        </section>
    </main>
</body>
</html>
