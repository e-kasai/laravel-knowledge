```php

// 代入許可一覧(一番推奨の書き方)
// ここに書いてないものは直接createやupdateで書き込み不可
// timestamps (`created_at`, `updated_at`) はEloquentが自動管理するので書かない
// idは主キーで自動採番なので書かない
// FKはSeeder や管理画面からrelation経由せず直接create()するような仕様の時のみ追加（relation経由想定なら書かない)
protected $fillable = [
        'name',
        'email',
        'password', // ただし保存前に必ずハッシュ化する 'password' => Hash::make($input['password'])
    ];

// guarded不可なし=全部のカラムが代入OK
protected $guarded = [];

// ここに書いたカラムだけを除外し残りは代入OK
protected $guarded = [
		'name'
		'password'
];

//全部拒否（これがデフォルト）
protected $guarded = ['*'];


// DBの中のデータ自体は消えない
// 保存されてるが「外に見せるとき（シリアライズ時＝モデルを JSON に変換する処理時）」に隠すだけ。  
protected $hidden = [
        'password',
        'remember_token',
    ];
 
// Laravel がデータベースとやり取りする際に、自動で型を変換
// コントローラやビュー側で毎回 `intval()` などの変換処理を書く必要をなくせる   
protected $casts = [
        'price' => 'integer',
        'is_sold' => 'boolean',
        'published_at' => 'datetime',
    ];


// `fillable` と `guarded` を両方書くと、`fillable` が優先される（通常併用は不可）


```
