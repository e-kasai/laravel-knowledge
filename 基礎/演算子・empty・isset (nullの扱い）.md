

# null演算子

左辺が null の場合は右辺を使う

$a が null なら $b を使う
```
$a ?? $b
```


パターン：
```
null → $b
''   → $a
0    → $a
false→ $a
[]   → $a
'abc'→ $a
```

→　null以外は全部左辺が採用


# エルビス演算子

```
$a ?: $b
```

こっちは：
空・0・false・' '・null すべてを “false” と扱う (nullも含む)


例：
```
''  → $b
0   → $b
null→ $b
false→$**b**
```


# empty($a)

$a が「空」なら true

PHPの「空」は以下が該当：
```
''（空文字）
0
'0'
false
null
[]
未定義の変数（※エラーにならず false扱い）
```

注意：
- 0 まで「空」と判定される
- 勤怠や金額計算などでは要注意 (ex: 0円→空扱い）



# isset($a)

$a が「存在していて null ではない」なら true

つまり：
```
0 → true
'' → true
false → true
null → false
未定義 → false
```


# まとめ

`$a` が各値の場合の比較：

| `$a` | `$a ?? 'B'` | `empty($a)` | `isset($a)` |
|---|---|---|---|
| null   | `'B'` | true  | false |
| ''     | `''` | true  | true |
| 0      | `0`  | true  | true |
| '0'    | `'0'` | true | true |
| false  | `false` | true | true |
| []     | `[]` | true | true |
| 未定義 | `'B'`（`$a ?? 'B'`） | true | false |
