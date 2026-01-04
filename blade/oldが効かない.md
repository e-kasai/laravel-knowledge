
---

#### パターン１：ブラケット記法のまま渡している（配列にありがち）
Laravel の `old()` は内部的に
`session()->getOldInput($key)`で値を取る。
`$key` には `breaks.0.break_in` のような ドット記法 が期待される

しかし、多次元配列などで、bladeではブラケット記法でかいていると、oldが拾えずスルーされる


### 解決方法1：ドット記法に変換して渡す

以下のように修正すると、確実に動作する

ex:
```php
<input
    type="time"
    name="{{ $row["name"][0] }}"
    {{-- value="{{ old($row["name"][0], $row["value"][0]) }}" --}}   ←ブラケット記法をやめる
    value="{{ old(str_replace(["[", "]"], [".", ""], $row["name"][0]), $row["value"][0]) }}" // ←ドット記法に直す
    />
```