---
tags:
  - "#cast"
  - "#boolean"
---

#### code
```php
protected $casts = [
        'is_approved' => 'boolean',    //if文で正しく条件分岐させるため
    ];
```

---

#### 解説

💡 たとえば、こんなDBデータがあるとします
id	  name	   is_approved
1	  山田	    1
2	  佐藤	    0

MySQLでは「true/false」という型がないので、
booleanの代わりに 0（偽）と1（真） が入っています。


1. キャストなし

Laravelでこれを読むと、
文字列（string）として扱われます。
```php
$attendance->is_approved; // "1" または "0"
```

つまり "0" でも if文では「true扱い」になってしまう！
（PHPでは空文字でない限り `true` とみなされるため）

---

2. キャストあり
```php
protected $casts = [
    'is_approved' => 'boolean',
];
```

こう書くと、Laravelが自動で 1 → true、0 → false に変換してくれます。


#### メリット　
```php
// $attendance->is_approved; は true または false

if ($attendance->is_approved) {
    echo '承認済み';
} else {
    echo '未承認';
}
```

こう書いたとき　1 のときだけちゃんと承認済みになって、0 のときは"未承認"になる。
つまり、trueとfalseが正しく認識される

---

### まとめ
boolean で真偽値をキャストをしておくと、Laravelが `0 or 1 → false or true` にしてくれて
条件分岐のバグ(falseなのに空文字じゃないのでtrue扱いされる）を防げる