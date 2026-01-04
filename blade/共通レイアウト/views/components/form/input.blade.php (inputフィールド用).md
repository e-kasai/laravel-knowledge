
---
#### 役割
- 個別の入力フィールド を作るコンポーネント
- label, input/textarea, バリデーションエラー表示を一式まとめる
- 単独でも使えるが、通常は `comonents/form/card.blade.php` と合わせて使う

#### PATH例
`view/components/form/input.blade.php`

---

#### メリット

input欄の見た目やバリデーションエラー表示を 一箇所で統一管理。
追加の属性（例：required や maxlength="10"）も　{{ $attributes }} で親から渡せる柔軟設計。

---

#### code

```html

//プロパティ受け取り
@props([
    "name",
    "label",
    "type" => "text",
    "value" => null,
    "autocomplete" => null,
])

@php
    $isPassword = $type === "password";   //このinputがパスワード入力かどうか」を判定しbool値をいれる
    $isTextarea = $type === "textarea";
    
    //パスワードなら → "off" に固定（ブラウザの自動入力を無効化）, それ以外なら → $autocomplete の値をそのまま使う
    $auto = $isPassword ? "off" : $autocomplete;

    //ex: id="email" が渡されていればそのまま, idがない場合 → name="email" を流用して id="email" にする
    $id = $attributes->get("id") ?? $name;
    
    // エラー表示用に、idの末尾に -error を付けた文字列を作っている
    $errorId = $id . "-error";
    $oldValue = old($name, $value);
@endphp

<label class="form-group">
    <span class="form-group__label">{{ $label }}</span>
    @if ($isTextarea)
        <textarea
            id="{{ $id }}"
            name="{{ $name }}"
            class="form-group__control @error($name) is-invalid @enderror"  //エラー時にCSSクラスを自動で切り替える
            @if($auto) autocomplete="{{ $auto }}" @endif
            @error($name)
                aria-invalid="true"  //スクリーンリーダー（音声読み上げツール）に対しこの入力欄は無効（エラー状態）と伝える属性
                aria-describedby="{{ $errorId }}"  //スクリーンリーダーがエラー文も一緒に読み上げてくれる
            @enderror
            {{ $attributes }}
        >
{{ $oldValue }}</textarea
        >
    @else
        <input
            class="form-group__control @error($name) is-invalid @enderror"
            {{-- 追記４：id --}}
            id="{{ $id }}"
            type="{{ $type }}"
            name="{{ $name }}"
            {{-- パスワード以外だけoldで保持 --}}
            @unless ($isPassword)
                value="{{ old($name, $value) }}"
            @endunless
            @if ($auto)
                autocomplete="{{ $auto }}"
            @endif
            {{ $attributes }}    [^1]
        />
    @endif
    @error($name)
        {{-- <span id="{{ $errorId }}" class="form-group__error">{{ $message }}</span> --}}
        <span id="{{ $errorId }}" class="form-error">{{ $message }}</span>
    @enderror
</label>
```

---
#### 使い方

たとえばユーザー登録フォームで
```hmtl
<x-form.input name="name" label="ユーザー名" />
<x-form.input name="email" label="メールアドレス" type="email" />
<x-form.input name="password" label="パスワード" type="password" />
<x-form.input name="profile" label="自己紹介" type="textarea" />
```
→ それぞれテキスト入力、メール入力、パスワード入力、テキストエリアに自動変換されます。

---

#### 役割説明

プロパティ受け取り
```html
@props([
    "name",                  //フィールド名 (name 属性の値 ex:`name=email`)
    "label",                 //ラベル文字列
    "type" => "text",        //デフォルトは text、password や textarea も可
    "value" => null,         //初期値
    "autocomplete" => null,  //自動補完の指定
])
```

---
動的な判定

```html
$isPassword = $type === "password";
$isTextarea = $type === "textarea";
$auto = $isPassword ? "off" : $autocomplete;  //パスワード欄なら自動補完オフ、それ以外なら指定があればその値を使う
$id = $attributes->get("id") ?? $name         //id は自動的に name属性 と同じになる ex: name="email" id="email"
$errorId = $id . "-error";
$oldValue = old($name, $value);               //old() で入力保持（バリデーション失敗時の戻り値）
```


---

ラベル / 入力欄

```
<label class="form-group">
    <span class="form-group__label">{{ $label }}</span>
    @if ($isTextarea)
        <textarea …>{{ $oldValue }}</textarea>
    @else
        <input … />
    @endif
</label>
```


```html
@error($name)
    <span id="{{ $errorId }}" class="form-error">{{ $message }}</span>
@enderror
```

バリデーションエラーがあれば form-error で表示。



[^1]: 🧩 1. $attributes とは？
	
	Bladeコンポーネントで使える 「自動的に渡される特別な変数」 です。
	コンポーネント呼び出し時に、定義していない属性 を受け取ると、全部 $attributes にまとめて入ります。
	
	例①：呼び出し側
	<x-form.input name="email" type="email" placeholder="メールを入力" class="input--large" />
	
	
	このとき、
	name と type はコンポーネント側で定義している（@propsで受け取ってる）けど、
	placeholder と class は 定義していない属性 ですよね。
	
	→ それらが自動的に $attributes にまとまって入ります。
	
	例②：展開側（コンポーネント内部）
	<input {{ $attributes }} name="{{ $name }}" type="{{ $type }}">
	
	
	Bladeがレンダリングすると、
	$attributes の中身が展開されて次のようになります👇
	
	<input placeholder="メールを入力" class="input--large" name="email" type="email">
	
	🧩 2. $attributes の便利機能
	✅ 属性をマージできる
	
	クラスなどを追加したい場合は .merge() が使えます。
	
	<input {{ $attributes->merge(['class' => 'form-group__control']) }}>
	
	
	呼び出し側で class="input--large" を指定しても、
	自動的に両方合体します：
	
	<input class="form-group__control input--large">
