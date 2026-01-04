
---

#### 役割
フォーム全体の共通レイアウトを定義するコンポーネントファイル
カード風のレイアウト、タイトル、CSRF、method偽装、アクションボタン領域をまとめる
入力欄そのものは持たない（ただの枠と並びの仕組み）

#### PATH例
`resources/views/components/form/card.blade.php`

---

#### code例

```html

{{-- 親ビューで <x-form.card> ... </x-form.card> の間に書かれた中身がここに入る--}}

@props(["title" => "", "action" => "#", "method" => "POST", "enctype" => ""])

<section class="form-card">
    {{-- ページごとに異なるフォームタイトルを簡単に変えられる --}}
    <h1 class="form-card__title">{{ $title }}</h1>

    <form class="form-card__contents" method="POST" action="{{ $action }}" enctype="{{ $enctype }}" novalidate>
        @csrf
        {{-- strtoupper は PHPの組み込み関数、文字列をすべて大文字に変換 --}}
        @if (strtoupper($method) !== "POST")
            {{-- @methodでPOST以外の場合にPUT や PATCH、DELETE などのHTTPメソッドを擬似的に送信 --}}
            @method($method)
        @endif

        <div class="form-card__inputs">
            {{ $slot }}
            {{-- ここに各画面の入力欄が入る --}}
            {{-- ex <x-form.input name="name" label="ユーザー名" /> --}}
            {{-- ex <x-form.input name="email" label="メールアドレス" type="email" /> --}}
        </div>

        <div class="form-card__actions">
            {{-- ここにBが入る、送信ボタンなどアクションの領域--}}
            {{-- ex <x-slot name="actions"> --}}
            {{-- ex <button type="submit" class="btn">送信</button> ←ここがactionsとして読み込まれる --}}
            {{-- </x-slot> --}}
            {{-- 注意点：actionsを使わないと反映されない --}}
            {{ $actions ?? "" }}
        </div>
    </form>
</section>
```

---
## 解説

### 1. 冒頭：props（受け取る値の宣言）
```php
@props(["title" => "", "action" => "#", "method" => "POST", "enctype" => ""])
```
| 変数名 | 意味 | 初期値 |
|--------|------|--------|
| $title | フォームのタイトル（例：ユーザー登録） | 空文字 |
| $action | フォーム送信先URL | # |
| $method | HTTPメソッド（POST, PUT, DELETEなど） | "POST" |
| $enctype | ファイル送信用の属性（例：multipart/form-data） | 空文字 |

→ 呼び出し側で `<x-form.card>` のように使うとき、title や action を渡せる

###  2. 外枠の構造
```php
<section class="form-card">
    <h1 class="form-card__title">{{ $title }}</h1>
```
`<section>` がカード全体。
`<h1>` にフォームタイトル（例：「会員登録」など）が入ります。

---
### 3. formタグ部分
```html
<form class="form-card__contents" method="POST" action="{{ $action }}" enctype="{{ $enctype }}" novalidate>
    @csrf
```
`@csrf` 共通レイアウトファイルで自動挿入してる

---
### 4. 入力部分とボタン部分（slot・actions）
```html
<div class="form-card__inputs">
    {{ $slot }}    // 親ビューファイルの <x-form.input の部分がここに挿入される
</div>

<div class="form-card__actions">
<!-- ここに B が入る -->
    {{ $actions ?? "" }}  //$actions が存在すればそれを表示、なければ代わりに空文字
</div>
```

`$slot` はフォームの中身（入力フィールド群）が入る場所。
`$actions` はボタン類（送信・キャンセルなど）を表示する場所。


---
### 親ビュー

```html
<x-form.card title="新規登録" action="{{ route('register') }}" method="POST" enctype="multipart/form-data">
    <!-- ↓ この部分が $slot に展開される -->
    <x-form.input name="name" label="ユーザー名" />
    <x-form.input name="email" label="メールアドレス" type="email" />
    <x-form.input name="password" label="パスワード" type="password" />

    {{-- B --}}
    <x-slot name="actions">
        <button type="submit" class="btn">登録</button>
    </x-slot>
</x-form.card>
```

---

### 最終的なblade
```html
<section class="form-card">
    <h2 class="form-card__title">新規登録</h2>
    <form class="form-card__contents" method="POST" action="http://example.com/register" enctype="multipart/form-data" novalidate>
        <input type="hidden" name="_token" value="...csrf...">

        <div class="form-card__inputs">
            <!-- x-form.input の中身がここに展開される -->
        </div>

        <div class="form-card__actions">
        <!-- x-slot name="actions"の中身がここに展開される -->
        </div>
    </form>
</section>
```

