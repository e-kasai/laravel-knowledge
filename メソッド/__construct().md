
---

# コンストラクタとは

クラスが new された瞬間に自動で実行される初期化用メソッド。
__construct はクラス定義に置かれるが、実行されるのはインスタンスが new された瞬間だけ。
new されなければ存在していても何も起きない。

new がやることは2段階
1. クラスの実体（インスタンス）を作る
2. コンストラクタがあれば呼ぶ = コンストラクタは任意


## コンストラクタの役割
- クラス生成時に必要な値を受け取る
- プロパティに値をセットする


## ふつうのメソッドとの違い

ふつうのメソッド
```php
class Dog {
    public function bark() {
        echo "ワン！";
    }
}
$dog = new Dog();
$dog->bark(); // → ワン！
```
 自分で呼び出さないと動かない。


コンストラクタ（__construct）

```php
class Dog {
    public function __construct() {
        echo "犬が生まれた！";
    }
}

$dog = new Dog(); // → new した瞬間に「犬が生まれた！」が出る
```
new しただけで、自動で呼ばれる　＝　自分で呼ぶ必要なし。


---

## コンストラクタが必要なパターン　→　渡す変数がある

例：bladeで変数を受け取り表示させたい

```php
// Mailableクラス
class PingMail extends Mailable
{
    public $transaction;

    public function __construct($transaction)
    {
        $this->transaction = $transaction;
    }

    public function build()
    {
        return $this
            ->subject('お知らせ')
            ->view('emails.ping')
            ->with([
                'transaction' => $this->transaction,
            ]);
    }
}

```

```html
// ping.blade.php
<p>{{ $transaction->item->name }}</p>
```


## コンストラクタが不要なパターン　→　渡す変数がない

```php
class PingMail extends Mailable
{
    public function build()
    {
        return $this
            ->subject('お知らせ')
            ->view('emails.ping');
    }
}
```
```html
<p>これはテストメールです。</p>
```


## コンストラクタがつかわれる代表的な場面

### ロジックで使いたいとき
```php
class PriceCalculator
{
    public $price;
    public $taxRate;

    public function __construct($price, $taxRate)
    {
        $this->price = $price;
        $this->taxRate = $taxRate;
    }

    public function total()
    {
        return $this->price * (1 + $this->taxRate);
    }
}
```

```php
// newされたときにコンストラクタが自動で呼ばれる
new PriceCalculator(1000, 0.1);
```



### 条件分岐に使うとき
```php
class ReportMail extends Mailable
{
    public $isUrgent;

    public function __construct(bool $isUrgent)
    {
        $this->isUrgent = $isUrgent;
    }

    public function build()
    {
        $subject = $this->isUrgent
            ? '【至急】レポート'
            : 'レポート';

        return $this
            ->subject($subject)
            ->view('emails.report');
    }
}
```



### サービスクラス
```php
class UserNotifier
{
    private $user;
    private $notifyType;

    public function __construct(User $user, string $type)
    {
        $this->user = $user;
        $this->notifyType = $type;
    }

    public function notify()
    {
        // 通知方法を切り替える
    }
}
```
Controller → Service に渡す材料


### 初期設定状態を持たせたいとき
```php
class CsvExporter
{
    private $delimiter;

    public function __construct(string $delimiter = ',')
    {
        $this->delimiter = $delimiter;
    }
}
```