
---

assertViewHas()

---
#### 役割

- コントローラから返される ビューに特定のデータが渡されているか を確認するために使う

---
#### ex：viewに'items'という値が渡されているか
```php
$response->assertViewHas('items');
```

#### ex: 渡された値の件数も確認する場合
```php
$response->assertViewHas('items', function ($items) {
    return $items->count() === 5;
});
```

---

#### 使いどころ

- 一覧ページテスト
ちゃんと $items が渡ってるか確認。

- 詳細ページテスト
$item がビューにあるかチェック。

- 検索結果ページテスト
検索条件付きで渡された $items の件数を検証。