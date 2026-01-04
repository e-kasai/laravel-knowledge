
`with()` は **Eager Loading（イーガーローディング）** と呼ばれる機能で、  
「関連モデルをまとめて先にロードする」ためのメソッドです。

---

### **1. どういう機能？**

通常、Eloquent でリレーションを呼ぶときは**必要になったタイミングでSQLが実行**されます。  
これを **Lazy Loading（遅延ロード）** といいます。

例：

`$profiles = Profile::all();  foreach ($profiles as $profile) {     echo $profile->user->name; // ← ここで毎回SQLが走る }`

この場合、プロフィールが100件あれば **100回SQL** が発行されます。  
これが **N+1問題**。

---

### **2. `with()` の動き**

`with('user')` を付けると、  
**関連データをまとめて最初に1回だけ取得** します。

`$profiles = Profile::with('user')->get();  foreach ($profiles as $profile) {     echo $profile->user->name; // ここではSQLはもう走らない }`

→ SQLは **最初の1回** だけで済むので、処理が速く、DB負荷も下がります。

---

### **3. 使いどころ**

- 一覧表示などで、**関連データを何件も同時に使う場合**。
    
- 例：プロフィール一覧、購入履歴一覧、コメント一覧など。


### **4. 使いどころ**


例えばTodoアプリを作っていて

todo-Categoryのように紐づけたいとする（それぞれのTodoに、どのカテゴリに属しているかの情報を持たせたい）

そんな時に使うのがwithメソッド

【準備】

・ Todoモデルにリレーションメソッド（ここではcategory)を定義する

・ belongsToでTodoがCategoryに属することを宣言する

public function category()

{

  return $this->belongsTo(Category::class);

}

このコードによって、「Todoの中からCategory情報が引ける」状態にまずはなる

【Categoryの情報を引く】

さて、ふつうにTodoを一覧で取るとこんな感じ：

$todos = Todo::all();

でもこれだと category の中身を取るたびにSQLが発行される　=　N+1問題

しかしwithでこう書くと…👇　SQLは2回で済む（todos + categories）

$todos = Todo::with('category')->get();

$todos の中身は：

[

    Todo {

        id: 1,

        new_todo: '牛乳を買う',

        category_id: 2,

        category: Category {

            id: 2,

            category_name: '買い物',

            ...

        }

    },

    ...

]

このように、Todo オブジェクト(子）の中に category （親）というプロパティが追加されてる！

じゃあどうやって取り出す？

@foreach ($todos as $todo)

   (Todo:  {{ $todo->new_todo }})

　 (カテゴリ：{{ $todo->category->category_name }} ?? 'カテゴリなし'）

@endforeach

このように使える！