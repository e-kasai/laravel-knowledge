---

### 意味
○○じゃない場合に = つまり`@if(!xxx)`と同じ

### code
```html
//ルート名がloginでなければpを返す
@unless (request()->routeIs('login'))
    <p>ログインページではありません</p>
@endunless
```
