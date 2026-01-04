
---

#### やりたいこと　

* 画像がS3かローカルかの判定をbladeではなくモデルアクセサで行う

** メリット **
* ビューの責務を削減（表示はビュー、判定はモデル)
* 複数のビューに重複がある状態を作らない

---

#### code
```php
//Item.php
use Illuminate\Support\Str;

public function getImageUrlAttribute(): string //参照名はimage_url
{
    return Str::startsWith($this->image_path, ['http://','https://']) //httpなどから始まるか？（条件）
        ? $this->image_path                     // httpから始まるURL(S3)ならそのまま返す（true)
        : asset('storage/' . $this->image_path); // 相対パスなら公開URLに変換(false)
}
```

```html
@foreach ($items as $item)
// おすすめ一覧
@foreach ($items as $item)
<div class="item-card">
	<a href="{{ route("details.show", $item) }}">
	     <img src="{{ $item->image_url }}" alt="{{ $item->item_name }}" />
         <p class="item-card__name">{{ $item->item_name }}
			@if ($item->is_sold)
				<span class="item-card__sold">SOLD</span>
			@endif
		</p>
	</a>
</div>
@endforeach

// マイリスト
@foreach ($myListItems as $myListItem)
	<div class="item-card">
	<a href="{{ route("details.show", $myListItem) }}">
		<img src="{{ $myListItem->image_url }}" alt="{{ $myListItem->item_name }}" />
		<p class="item-card__name">{{ $myListItem->item_name }}
		@if ($myListItem->is_sold)
			<span class="item-card__sold">SOLD</span>
		@endif
		</p>
	</a>
	</div>
@endforeach
```

** point **
* getImageUrlAttribute() は そのモデルインスタンス（$this）専用。
* 各 $item / $myListItem がそれぞれ image_url を返すから衝突しない。
* Bladeでは一時変数を作らず、各行で ->image_url を読むだけにするのが安全・DRY。

---
### おさらい

#### アクセサとは

* 役割：DBに入ってる値を「人間に見せる用」に変換する仕組み
* 書き場所：モデルクラス（app/Models/...）の中、リレーションの下
* bladeでの呼び出し方：$model->property_name で使える

---

####  命名ルール(重要)

形式：get{Name}Attribute()
参照名：snake_case に変換される。

`getConditionLabelAttribute` → `$item->condition_label`
`getFullNameAttribute` → `$user->full_name`

例）get`ConditionLabel`Attribute(モデル側) → $item->`condition_label`(blade側)

---
