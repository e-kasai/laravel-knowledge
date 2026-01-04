
---

#### メール認証の流れ

1. ユーザー登録
User::create([...]) で新しいユーザーがDBに保存される。

2. イベント発火
`event(new Registered($user));`
ここで Registered イベントのインスタンスが作られて、Laravelのイベントシステムに渡される。

3. イベントディスパッチャの処理
`EventServiceProvider::$listen` を参照して、
「Registeredイベントが来たら、`SendEmailVerificationNotification` を呼び出す」と判断。

4. リスナー実行
`SendEmailVerificationNotification::handle(Registered $event)` が実行される。
この中で `$event->user->sendEmailVerificationNotification()` が呼ばれる。

5. メール送信
`sendEmailVerificationNotification()` が通知システムを通してメールを送る。

---

#### code

1. Userモデル
 ユーザーが「メール認証対象」かつ「通知可能」であることを示す
```php
// app/Models/User.php
use Illuminate\Contracts\Auth\MustVerifyEmail;

class User extends Authenticatable implements MustVerifyEmail //このUserはメール認証が必要なユーザーとLaravelに伝えるマーカー
{
     use Notifiable; // Laravelの「通知機能（Notification）」を使えるようにする
}
```

---

2. Fortify.php
「登録とメール認証を有効にする」
features = 認証関連の機能をオンにする設定などをここに書く
```php
// config/fortify.php
'features' => [
    \Laravel\Fortify\Features::registration(), //これが無いと Fortify の登録フロー（Registeredイベント発火）が動かない
    \Laravel\Fortify\Features::emailVerification(), // ←必須(リスナー）
],
```

---
3. EventServiceProvider
イベントとリスナー（= 実際の処理）をひもづける
```php
app/Providers/EventServiceProvider.php

//ここが無い イベント発見を無効化してると通知が送られない
protected $listen = [
    \Illuminate\Auth\Events\Registered::class => [                          // イベントクラスの読み込み宣言
        \Illuminate\Auth\Listeners\SendEmailVerificationNotification::class, //実際に「確認メールを送る処理」を実行
    ],
];
```

---

#### 自分でイベント発火する場合(イベント発火が無効の場合)

上記１～３に加え↓も必要

 RegisterController 内で明示的に
```php
use Illuminate\Auth\Events\Registered;
event(new Registered($user));  //new Registered($user) = イベントオブジェクトの作成

//event = Laravelのイベント発火ヘルパ関数。
//event($インスタンス) を呼ぶと、システムに「このイベントが起きたよ」と通知する
```
を呼ぶ

これで「登録 → イベント発火 → リスナー → 認証メール送信」という標準ルートが走る。


