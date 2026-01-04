
---

### withValidator() と　rulesとの違い

#### withValidator() 
-  整合性などの動的検証を行う
ex: 「休憩Aと休憩Bが重なっている」などは、入力値同士の関係を見て判断する等
- `withValidator()` の中で直接 `$validator->errors()->add()` する

```php
 if ($current['break_out'] > $next['break_in']) {
        $validator->errors()->add(
            "breaks.{$i}.break_in",
            "休憩時間が重複しています（{$current['break_in']}〜{$current['break_out']} と {$next['break_in']}〜{$next['break_out']}）"
        );
        break;
    }
```

#### rules()
- 形式のチェックなどstaticな検証を行う
- error messagesはまとめて `messages()` に置く
(`messages()` は`rules()` に書いた固定ルールに対応し、静的なルール専用)

---

### 置き場所

`authorize() → rules() → withValidator() → messages()`
rulesの後が通例



