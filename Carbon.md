---
tags: carbon
---

####  Carbonとは？
Laravelに標準で入っている「日付と時間の便利ツール」。
PHPの DateTime を使いやすくしたもの。


#### よく使うメソッド一覧

| メソッド | 目的 | 例（出力） |
|-----------|------|-------------|
| now() | 現在日時を取得 | 2025-10-21 13:45:00 |
| today() | 今日の日付だけ取得 | 2025-10-21 |
| parse('2025-10-01') | 文字列→日付に変換 | 2025-10-01 |
| addDay() / subDay() | 日付を1日進める / 戻す | 2025-10-22 / 2025-10-20 |
| addMinutes(30) | 分単位で加算 | 13:30 → 14:00 |
| diffInMinutes($other) | 時間差を分で取得 | 120 |
| format('Y/m/d H:i') | 指定書式で出力（英語） | 2025/10/21 14:00 |
| translatedFormat('m/d(D)') | 指定書式＋日本語化 | 10/21(火) |
| locale('ja') | 言語を設定 | 日本語で曜日表示など |


### よくある使い方
```php
use Carbon\Carbon;

// 現在日時
$now = Carbon::now();

// 1週間前
$lastWeek = Carbon::now()->subWeek();

// 曜日付きフォーマット
$jp = Carbon::parse('2025-10-21')->locale('ja')->translatedFormat('m/d(D)');

```

---

###  Laravelでの使われ方
モデルの `$casts に 'date' => 'datetime'` を書くと、自動で Carbonインスタンスになる
つまり `$attendance->date->addDay()` のような書き方ができる


---

#### フォーマット形式

| 記号 | 意味 | 例 |
|------|------|----|
| d | 日（2桁） | 21 |
| D | 曜日（短） | 月 |
| dddd | 曜日（長） | 月曜日 |
| m/d(D) | よく使う形式（月/日(曜)） | 10/21(月) |


format() → 英語で出る
translatedFormat() → 日本語になる