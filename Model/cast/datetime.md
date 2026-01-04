---
tags:
  - "#cast"
  - "#datetime"
---

#### code
```php
protected $casts = [
        'clock_in' => 'datetime:H:i',   //時刻計算・整形出力しやすくする為
        'clock_out' => 'datetime:H:i',
        'approved_at' => 'datetime',
    ];
```

---

#### 前提

1. DBで`timestamp` 型の場合 
特定のカラム（以下の３つ）は自動的に Carbon オブジェクト化してくれる　(castに書かなくていい）
```php
created_at
updated_at
deleted_at
```
→　上記のカラム以外はcastしないと文字列のままになるので、　自分で　`datetime`でキャストする


2. DBで`time`型の場合　
自動でCarbonオブジェクト化されない（文字列のまま）
→ キャストしないと文字列なので計算などが出来ずエラーになるので、　`datetime:H:i:s` などでキャスト する


---
#### メリット

- 時間計算が簡単（キャストなしだと文字列で計算できずエラー）
```php
$diff = $attendance->clock_out->diffInMinutes($attendance->clock_in);
```

- Bladeでそのまま `{{ $attendance->clock_in }}` と書いても "09:30" と表示される
- MySQLの TIME 型は秒を持っているが、 castで `'datetime:H:i'`  にしておけば、表示時に秒が出なくてスッキリ

