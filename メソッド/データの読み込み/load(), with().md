
---

# load() とは

すでに取得しているモデルに、関連データを後から読み込む命令

## 例: loadでデータの読み込み
```php
$transaction->load([
    'item',                           //$transaction->item がすぐ使えるようになる
    'messages.user.profile',          //ドットでつながったリレーション指定
]);
```

```php
取引
 └ メッセージ
    └ ユーザー
       └ プロフィール
```

## 例:取得したデータの使い方

```html
$transaction->item->item_name;

@foreach ($transaction->messages as $message)
    {{ $message->user->profile->avatar_path }}
@endforeach
```


---
# with()とは

モデルを取得するときに、関連データもまとめて取得するメソッド

## withでのデータ取得
```php
Transaction::with('item')->find($id);

//ドットでつながったリレーション指定もOK
 Transaction::with([
    'item',
    'messages.user.profile',
])->find($id);
```

## 取得したデータの使い方

OKパターン
```html
{{ $transaction->item->name }}
```

NGパターン
```html
{{ $item->name }}   // $item という変数が存在しないので使えない
```

もし `$item->name` と書きたいならController側で、変数定義して渡す必要がある
```php
$item = $transaction->item;
return view('xxx', compact('item'));
```

```html
// blade側
{{ $item->name }}
```
$item という変数が存在するので使えるようになった

---
# withとloadの違い

with()  ＝　クエリビルド段階で一緒に読む
最初から関連が必要と分かっているとき → with()
```php
Transaction::with('item')->find($id);
```

load()   ＝　取ったあとに追加で読む
処理途中で、やっぱり必要になったとき → load()
```php
$transaction = Transaction::find($id);
$transaction->load('item');
```


---
# 使わないとどうなるか　＝ N+1問題

たとえば Blade でこう書いた場合
```html
{{ $transaction->item->item_name }}

@foreach ($transaction->messages as $message)
    {{ $message->user->profile->avatar_path }}
@endforeach
```

このとき起きることは：
1. 最初に $transaction を1回取得
2. item を使った瞬間に DBへ1回
3. messages を使った瞬間に DBへ1回
4. 各 message ごとに
user → 1回
profile → 1回

メッセージ数が増えるほど、DBアクセスが爆発的に増える　＝つまり N+1問題でパフォーマンスが落ちる