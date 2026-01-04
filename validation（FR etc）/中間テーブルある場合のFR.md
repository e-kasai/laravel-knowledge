
---
##  多対多カテゴリのバリデーション（ex:`items` × `categories`）

- `items`：`categories` は多対多。中間テーブル：`category_item`。
    
- 画面はチェックボックス想定：`name="category_ids[]"`。
    

## FormRequest（新規作成のみ）

```php
public function rules(): array {     
	return [         
		//1つ目はcategoryの配列全体に対してのルール
		'category_ids'   => ['required', 'array', 'min:1'],
		//2行目は配列の中の各要素に対してのルール
		'category_ids.*' => ['integer', 'distinct', 'exists:categories,id'],     
		]; 
}
```

### ルールの意味

- `required`：最低1件は必須。
    
- `array`：配列で送信されていること。
    
- `min:1`：要素数が1以上ある。
    
- `category_ids.*`：配列の各要素に適用。
    
- `integer`：各要素が整数。
    
- `distinct`：重複禁止（同じカテゴリがないこと）。
    
- `exists:categories,id`：`categories`テーブルの`id`カラムに実在する。
    

## `exists:categories,id` の内訳

- `categories`＝テーブル名。
    
- `id`＝カラム名（主キー）。
    
- ソフトデリート除外したい場合：
    

`'exists:categories,id,deleted_at,NULL'`



## `category_ids.*` の「*」とは

- ワイルドカード（配列の各要素）。
    
- 例：`[1,3,5]` の 0,1,2 番目に同じルールを適用。
    

## コントローラ側（紐付け）

```php
// 作成 
$item = auth()->user()->items()->create($validatedItemData); 
$item->categories()->sync($validated['category_ids']); // attachでもOK
```

## 将来：編集機能を追加するなら

- 変更があった時だけチェックしたい場合は `required` → `sometimes` に。
    

```php
public function rules(): array {     
		return [         
		'category_ids'   => ['sometimes', 'array', 'min:1'],         
		'category_ids.*' => ['integer', 'distinct', 'exists:categories,id'],     
		]; 
	}
```

## 入力例（送信データ）

```json
// JSONイメージ 
{   "item_name": "Tシャツ",   "price": 1200,   "category_ids": [1, 3, 5] }
```

## チェックポイント

- 中間テーブルのカラム自体は**バリデーションしない**。
    
- 送られてくるのは「ID配列」。
    
- DB反映は `attach` / `sync` で行う。
    
- 新規のみなら上のFormRequestで十分。