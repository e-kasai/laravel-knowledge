### validation error messageの優先順位

1. 　FormRequest の `messages()` メソッド　←１番強い
2. 　翻訳ファイル（`resources/lang/ja/validation.php`）の定義　※FRに見当たらないとき
3. 　Laravelのデフォルトメッセージ（英語）※上２つどちらにもない時


### validation error messageの適用範囲
- Form Request : そのFRの範囲のフォーム（例：登録ページのみ）
- validation.php : フォーム全体（例：全部のページ）