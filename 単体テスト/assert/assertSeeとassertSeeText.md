
---

### assertSee

- 役割：「レスポンスHTMLのソース」に文字列が含まれているかを確認。
- 特徴：タグやエスケープされた文字もそのまま対象になる。
- HTMLタグ込みや属性込みで存在チェックしたいとき

例：検索に
```php
// 1) ホームで検索（GET）
        $this->get('/?keyword=検索')->assertOk();
        // 2) マイリストに遷移（クエリは引き継ぐ設計）
        $response = $this->actingAs($user)->get(
            route('items.index', ['tab' => 'mylist', 'keyword' => '検索'])
        );
 // 期待：検索欄に value="検索" が残っている（属性一致はエスケープ無効でチェック）
	$response->assertOk();
	$response->assertSee('value="検索"', false);    //←この部分
	$response->assertSeeText('検索にヒットする商品');
	$response->assertDontSeeText('ヒットしない商品');
```

**  違い **
HTMLレスポンスが：`<input type="text" name="keyword" value="検索">` のとき
✅ `assertSee('value="検索"', false)` → 通る
❌ `assertSee('value="検索"')` → 通らない（探す文字列が `value=&quot;検索&quot;` に変換されてしまいHTMLと一致しないから）
❌ `assertSeeText('value="検索"')` → 通らない（input属性は“テキスト”じゃないから）

---

### assertSeeText

- 役割：「実際にブラウザで見えるテキスト」にその文字列があるかを確認。
- 特徴：HTMLタグや属性は無視して、テキスト部分だけを拾う。
- 用途：ユーザーに見えるテキストを確認したいとき

例：
```php
$response->assertSeeText('SOLD');
// <span class="item-card__sold">SOLD</span> があっても「SOLD」という文字が出ていれば成功
```

---
### エスケープとは

**「文字が HTML として誤解されないように、安全な形に変換すること」**

#### {{ $変数 }}（安全・エスケープあり）
- 中身が HTML っぽくても「ただの文字」として表示される。
- HTMLタグは効かない。
- セキュリティ上基本はこっち。

例：
```php
$text = "<b>太字</b>";
```

```html
{{ $text }}
```

→ ブラウザに見えるのは普通の太さの文字、タグは働かないでそのまま文字として出る
`<b>太字</b>`

---

#### {!! $変数 !!}（危険・エスケープなし）

- 中身をそのまま HTML として出力する。
- タグが効くから見た目を変えられる。
- 悪意あるスクリプト（XSS）も動いちゃう危険あり。

例：

`{!! $text !!}`

→ ブラウザに見えるのは 太字（実際に `<b>` が動く）
`$text = "<b>太字</b>"` なら、そのまま `<b>太字</b>` となって、ブラウザでは「太字」に見える。

#### まとめ
- {{ }} = 必ず安全に文字列として出したいとき（ほぼ常用）
- {!! !!} = 自分でHTMLを組み込みたいときだけ（例えば記事本文に <p> を含めたいとき）