
---

1. 何をするもの？

* 現在のURL（クエリ付き） で取得できる 
* 指定したクエリパラメータを追加／上書きした新しいURLを返す関数。

---

2. 基本の書き方

`request()->fullUrlWithQuery(['key' => 'value'])`

3. 使用例

現在URLが`/items?keyword=時計&page=2` だったとする

```blade
<a href="{{ request()->fullUrlWithQuery(['tab' => 'recommend']) }}">
    おすすめ
</a>

```

↑のように書くと`request()` = `/items?keyword=時計&page=2`　に `tab=recommend` が追加される↓
`/items?keyword=時計&page=2&tab=recommend`

---

4. よくある使い道

検索フォーム＋ページネーション
→ ページ送りしても検索条件を残したいとき

タブ切替リンク
→ 検索条件を残したままタブを切り替えたいとき

---

5. メリット

手動で ?tab=...&keyword={{ request('keyword') }} を書かなくていい

パラメータが空なら &keyword= が付かない

既存のクエリ（page, sort, keywordなど）を自動で保持してくれる

---

6. 注意点

* URL文字列を返すだけで、コントローラの処理には関与しない
* ページネーション時は別途 ->withQueryString() が必要
* 引数無しはNG
`request()->fullUrlWithQuery()` をカッコだけで呼ぶと エラー になる
 これは「最低でも1つはクエリを指定してね」という設計だから


---

使い分けの整理：

ページ送りリンク → withQueryString()
タブ切替リンク → fullUrlWithQuery()
引数無しがいい&フルURLをクエリ含めて取得したい　→ fullUrl()