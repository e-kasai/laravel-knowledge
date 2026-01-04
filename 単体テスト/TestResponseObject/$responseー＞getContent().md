
---

LaravelのFeatureテストで $this->get(...) や $this->post(...) を呼ぶと、Illuminate\Testing\TestResponse というオブジェクトが返ってくる

getContent() はその中の 「レスポンスの生の本文（HTML文字列）」を取り出すメソッド

---

```php
$response = $this->get(route('details.show', $item));

// $response->getContent() は HTML 全文（<html>...</html>）が文字列で返る
$html = $response->getContent();

$this->assertMatchesRegularExpression(
    '/<span\s+class="count">\s*1\s*<\/span>/',
    $html
);
```

このとき $html の中には実際のBladeがレンダリングされた結果（アイコンやspan、商品名など全部入り）が丸ごと文字列で入ってる

---
#### 他メソッドとの使い分け

assertSeeText() は「見えているテキストノード」を探すだけ。

assertSee(..., false) は「HTML全体の生文字列」にマッチ。

もっと細かく「タグ構造やclass名までチェックしたい」場合は、getContent() を取って正規表現で検証する