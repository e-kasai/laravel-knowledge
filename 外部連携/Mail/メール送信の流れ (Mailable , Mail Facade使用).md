---
tags:
- Mailable
- MailFacade
- メール送信
---

## 全体の流れ

1. きっかけが発生 
- 例：取引完了、登録完了、更新完了 など  

2. Controller
- メールを送信するかどうか判定する
- メールファサード`Mail::to()->send()` を呼ぶ 

3. Mailファサード
- 送信先と使用する Mailable を指定され、送信を実行する 
- 送信の仕組み自体は Laravel の担当

4. Mailable = メール内容を定義  
- 件名（subject）
- 使用する Bladeファイル
- Blade に渡すデータ

5. Bladeで本文を表示  
※メール用 Blade は resources/views/emails/ にまとめるのが一般的（慣習）



## 役割の分担

Controller   ：送信条件と宛先を判断し、送信を指示する  
Mailファサード ：指定された送信先と Mailable を使って送信を実行  
Mailable      ：メール内容を定義  
Blade          ：メール本文を表示


## まとめ
- メールは自動では送られない  →　Controller が明示的に Mail Facadeに命令している
- メールの中身は Mailable に切り出すことで保守性が高くなる

