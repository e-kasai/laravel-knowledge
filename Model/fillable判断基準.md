
---

## 基本的な判断基準

- $fillable は「フォームなど外部から入力を受け取ってもOKなカラム」を登録するもの。
- ユーザーが直接変更できない値 は基本的に外すのが安全。

| カテゴリ | 例 | $fillable に入れる？ | 理由 |
|-----------|----|------------------|------|
| 🧍‍♂️ ユーザー入力 | comment, new_clock_in, new_clock_out | ✅ 入れる | フォームから入力されるデータだから |
| ⚙️ システム側で自動決定 | requested_by, approval_status, approved_at | ❌ 外すのが安全 | ログイン中ユーザーや承認処理でサーバ側が決めるため |
| 🔗 外部キー（親を明示） | attendance_id, break_time_id | ✅ 入れる | 関連データを明示的に紐づけるため必要 |

---

## コントローラーで値をいれる場合

### 結論：コントローラーでの代入方法による

1.  `「update()」`や`「create()」`で代入するなら `$fillable` に入れないとNG
2.  `$model->〇〇 = 値`で代入するなら `$fillable` に入れなくてOK


### 具体的な比較例

###  `$fillable` が必要なケース

1. コントローラで 配列でまとめて代入(mass assignment) する書き方

```php
UpdateRequest::create([
    'attendance_id' => $attendance->id,
    'requested_by'  => Auth::id(),
    'comment'       => $request->input('comment'),
    'approval_status' => 0,
]);
```

🟢 この書き方では、
Laravelが内部で「一括代入（mass assignment）」をしている ため、
セキュリティチェックとして $fillable に登録しておく必要がある。


2. `request()->only` で代入する書き方
```php
$requestData = new UpdateRequest($request->only(['attendance_id', 'comment', 'new_clock_in', 'new_clock_out']));
$requestData->requested_by = Auth::id();       // 手動代入
$requestData->approval_status = 0;             // 手動代入
$requestData->save();
```

---

#### `$fillable` が不要なケース

3. プロパティを1つずつ直接代入する書き方

```php
$requestData = new UpdateRequest();
$requestData->attendance_id = $attendance->id;
$requestData->requested_by  = Auth::id();
$requestData->comment       = $request->input('comment');
$requestData->approval_status = 0;
$requestData->save();
```


🔵 この書き方では `$fillable` に関係なく代入できる。
なぜなら「一括代入」ではなく「個別代入」だから。

