
---

### 使用例


1. テキストと画像を横に並べるとき

デフォルトでは画像の下端が文字のベースラインに合ってしまう
`vertical-align:middle;`を指定すると文字と画像の中央がそろう

```html
<img src="icon.png">
```

```css
img {
  vertical-align: middle;
}
```

---

2. テーブルの場合 = 「セルの高さに対して、内容を上下中央に寄せる」

テーブルのセル `<td> や <th>` でもよく使われます。

```css
td {
  vertical-align: middle;
}
```

つまり、セルが高くても、文字が真ん中に配置されます。