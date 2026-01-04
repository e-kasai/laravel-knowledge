---
Tags:
- Facade
- Mail
- Mailable
---

## Mail ファサードとは

Laravel が用意している 「メール送信を指示するための入口」

- メールを送る命令を出す役割
- 実際の送信処理（SMTPなど）は中で隠されている

## 役割
- 送信先を指定する
- どの Mailable を使うか指定する
- 送信処理の開始点になる

※ メールの中身は作らない  
※ 件名・本文は決めない
※ 送信先は決めない（コントローラーが決めた送信先に送れと指示するだけ）

## 基本の使い方

```php
Mail::to($email)->send(new XxxMail($data));
```

---
## 変えてはいけない部分(Laravelの型)

```php
Mail::to()->send()
```

## プロジェクトごとに変わる部分

1. $email（=送信先）
`Mail::to($email)`
あて先は管理者のメール、登録ユーザーのメールなど案件ごと・画面ごとに変わる。

2. XxxMail（=Mailableクラス名）
`new XxxMail(...)`
例：TransactionCompletedMail, PasswordChangedMailなど、用途ごとにmailableクラス名は変わる。

3. $data（=中身に使うデータ）
`new XxxMail($data)`
例：　$transaction , $userなど任意の情報に変わる。


## 実際の例

コントローラー内でメールFacadeを呼び出している
```php
// TransactionCompleteController.php
// 出品者にメール送信
        Mail::to($transaction->item->seller->email)
            ->send(new TransactionCompletedMail($transaction));
```

※関連
[[Mailableクラス]]