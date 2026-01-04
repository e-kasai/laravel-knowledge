---
Tags: "#Mailable"
---

## Mailable とは

Laravel における「メール1通分の内容を定義するクラス」、以下３つを定義する。

- メールの件名
- どの Blade を使うか
- Blade に渡すデータ

※ 送信処理そのものは行わない。
※ 送信先の決定はコントローラが担当


## Mailableクラスの作成場所

```php
# コントローラーやモデルと同じレイヤー
app/
 └─ Mail/
     └─ XxxMail.php
```

## Mailableクラスの中身(例)

```php
<?php

namespace App\Mail; //名前空間はApp\Mail

use Illuminate\Mail\Mailable;

class TransactionCompletedMail extends Mailable
{
    public $transaction;

    public function __construct($transaction)
    {
        $this->transaction = $transaction;
    }

    public function build()
    {
        return $this->subject('取引が完了しました')
            ->view('email.transaction_completed')
            ->with([
                'transaction' => $this->transaction
            ]);
    }
}
```

※関連
[[__constructの役割]]

## 何をするか (各メソッド）

- build()        　 =  メールの完成形を定義する
- subject()       =  メール件名を設定
- view()           =  本文テンプレートのblade fileを指定
- with()            = 表示用データを渡す

---

## 使われる流れ

Controller からMailファサード経由で、以下のように呼ばれる。 `XxxMail` が Mailableクラス。

```php
Mail::to($email)->send(new XxxMail($data));
```


## ポイント

- メール内容を Controller から切り離せる　＝ 再利用しやすい
- メール以外の処理を書かない

---






