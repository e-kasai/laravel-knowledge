
---

### ex
```php
// 休憩（break_in / break_out）
            'breaks.*.break_in' => [
                'nullable',
                'date_format:H:i',
                'required_with:breaks.*.break_out',
                'after_or_equal:clock_in',
                'before:clock_out',
            ],
            'breaks.*.break_out' => [
                'nullable',
                'date_format:H:i',
                'required_with:breaks.*.break_in',
                'after_or_equal:breaks.*.break_in',
                'before_or_equal:clock_out',
            ],
```

### 解説
 -`breaks`            → フォームの配列名
- `*`                 → すべてのインデックス（0, 1, 2...）
- `break_in`        → 各配列要素のキー名

#### イメージ
具体的には、フォームの送信データがこんな構造になっている場合を想定
```php
'breaks' => [
    ['break_in' => '09:00', 'break_out' => '09:15'],
    ['break_in' => '12:00', 'break_out' => '12:45'],
]
```

このとき `breaks.*.break_in` は
「`breaks`配列の中のすべての要素にある `break_in` キーをチェックする」 という意味

### 注意点

`breaks.*.break_in` の `* `は “配列のインデックス” を表す。
だから実際のエラーメッセージは
`breaks.0.break_in` や `breaks.1.break_out` のようなドット記法で格納される。

Bladeでは `breaks[0][break_in]` を使っているため、ブラケット形式→ドット記法への置換が必要。

#### ブラケット形式→ドット記法への置換
```html
@props(["rows" => [], "isEditable" => false])

<div class="detail-table">
    <table class="detail-table__inner">
        <tbody>
            @foreach ($rows as $row)
                <tr>
                    <th class="detail-table__label">{{ $row["label"] }}</th>
                    <td class="detail-table__value">
                        @if ($isEditable && isset($row["name"]))
                            @php
                                // ドット記法に変換（例：breaks[0][break_in] → breaks.0.break_in）
                                // $row["name"] の中の1つ1つ（$name）について、[ を . に、  ] を空文字   に置き換え
                                $errorKeys = is_array($row["name"])
                                    ? array_map(fn($name) => str_replace(["[", "]"], [".", ""], $name), $row["name"]) 
                                    : [str_replace(["[", "]"], [".", ""], $row["name"])];
                            @endphp

                            {{-- 出退勤・休憩 --}}
                            @if ($row["type"] === "time-range")
                                <div class="detail-table__time-range">
                                    <input type="time" name="{{ $row["name"][0] }}" value="{{ old($row["name"][0], $row["value"][0]) }}" />
                                    <span class="detail-table__tilde">〜</span>
                                    <input type="time" name="{{ $row["name"][1] }}" value="{{ old($row["name"][1], $row["value"][1]) }}" />
                                </div>
                            @elseif ($row["type"] === "textarea")
                                <textarea name="{{ $row["name"] }}">{{ old($row["name"], $row["value"]) }}</textarea>
                            @endif

                            {{-- 各項目に対応するエラー表示 --}}
                            @foreach ($errorKeys as $key)
                                @error($key)
                                    <p class="form-error">{{ $message }}</p>
                                @enderror
                            @endforeach
                        @else
                            {{ $row["value"] }}
                        @endif
                    </td>
                </tr>
            @endforeach
        </tbody>
    </table>
</div>
```

#### 解説

`array_map(fn($n) => str_replace(["[", "]"], [".", ""], $n), $row["name"])`
はこういう意味：

`$row["name"]` の中の1つ1つ`（$name）`について、
"[" →　 "."   に、
"]"  →　 "空文字" に　　置き換えて返す。

⚙️ 例
```php
$row["name"] = ["breaks[0][break_in]", "breaks[0][break_out]"];
```
これを通すと：

```php
$errorKeys = [
    "breaks.0.break_in",
    "breaks.0.break_out"
];
```


### 動作イメージ

`array_map()` が `$row["name"]` の要素を1つずつ取り出す

取り出した要素が `$n` に渡される

それぞれ `str_replace()` で置換される

つまり、こう動いてる：
```php
// 実際の内部イメージ
[
  str_replace(["[", "]"], [".", ""], "breaks[0][break_in]"),
  str_replace(["[", "]"], [".", ""], "breaks[0][break_out]")
]
```


結果：
```
["breaks.0.break_in", "breaks.0.break_out"]
```

### まとめ
変数	                    何を表すか	                                          どこで使われるか
$row['name']	      入力フォームの name 属性の配列全体	          array_map の「第2引数」
$n	                 その配列の要素1つずつ（ループ内で使われる）	   fn() の「引数」

つまり：
`$row["name"]` は ループの対象全体、
`$n` は ループ中の1要素。 「今処理しているやつ」専用の一時変数
これが違い。
