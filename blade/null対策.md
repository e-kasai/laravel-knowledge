
---

### 三項演算子版
{{ $attendance->date ? $attendance->date->format('Y-m-d') : '' }}


$attendance->date が null なら空文字を返す。

nullでなければ format() を実行。

ただし $attendance 自体が null の場合はエラーになる。
（例： $attendance = null のとき → Trying to get property 'date' of non-object）


---
### optional() ヘルパー版
{{ optional($attendance->date)->format('Y-m-d') }}


optional() は null安全演算子のようなもの。

$attendance->date が null でも、format() を呼んでも nullを返すだけでエラーにならない。

$attendance 自体が null でも安全に動く。

つまり「どこが null かわからない」状況でも落ちない。