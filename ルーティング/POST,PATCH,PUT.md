
---

🟦 POST（作成）

都度新しいレコードが増える（新規作成）
RESTでは「create」に相当します。

```php
Route::post('/attendance', [AttendanceController::class, 'store']);
```
---

🟨 PATCH（部分更新）

既に存在するリソースの「一部だけ更新」。
→ 例：/attendance/detail/{id} に「退勤時刻だけ」送る。

レコードの一部を変更する。

Laravelではフォームに次のように書いて指定します。
```blade
<form method="POST" action="/attendance/detail/{{ $attendance->id }}">  //GETとPOSTしか送れないので次の行で疑似的にPATCH
    @method('PATCH')
    @csrf
    <input name="end_time" type="time">
    <button type="submit">退勤</button>
</form>
```

```php
use App\Http\Controllers\AttendanceController;

Route::patch('/attendance/detail/{id}', [AttendanceController::class, 'update']) //patch
    ->name('attendancedetail.update')
    ->middleware('auth');
```

これで実際にはブラウザからPOSTされますが、Laravel側でPATCHとして扱われます。

---

🟥 PUT（全体更新）の場合

「レコード全体を送る」想定。
つまり、送ったデータで丸ごと置き換える＝リソース全体をその内容で置き換える（replace）

```php
{
  "id": 1,
  "user_id": 3,
  "start_time": "09:00",
  "end_time": "18:00",
  "memo": "出社に変更"
}
```

➡ サーバはこのJSONで既存データを全て上書きします。
もし memo しか送らなかった場合、他の値（start_timeやend_time）が「空」として上書きされる可能性があります。

---