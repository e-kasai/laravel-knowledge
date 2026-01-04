---

### ログイン済みユーザーとguestで条件分岐する

```html
<nav class="header__nav">
                    {{-- ログイン時はログアウト、ゲストにはログインを表示 --}}
                    @auth
	                    //ログイン済みユーザーへの処理
                        <form class="logout-form" action="{{ route("logout") }}" method="POST">
                            @csrf
                            <button class="logout-form__btn" type="submit">ログアウト</button>
                        </form>
                    @else
	                    //guestの場合の処理
                        <a class="nav__link" href="{{ route("login") }}">ログイン</a>
                    @endauth
					//ここからはどちらのパターンでも表示
                    <a class="nav__link nav__link--mypage" href="/mypage">マイページ</a>
                    <a class="nav__link nav__link--sell" href="/sell">出品</a>
                </nav>
```

