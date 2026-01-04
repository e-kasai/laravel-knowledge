
---

## role
すべての now() が同じ固定日時を返すようにする

## ex: issue
```php
$date = now()->toDateString();
$attendance = Attendance::factory()->create([
    'clock_in' => now(),
    'date' => $date,
]);
```

この2行の実行には、ほんの数ミリ秒〜数秒の差があります。
そのため、ごく稀にこんなことが起こります。

$date	2025-10-15
$attendance->clock_in	2025-10-14 23:59:59（別日扱いになる）

→ こうなると「同じ日付のはずなのに不一致」と判定され、テストが失敗します。

---
### ex: 基本コード

```php
Carbon::setTestNow('2025-10-15 09:00:00');

//どのマシン・どのCI環境でも isoFormat('YYYY年M月D日(ddd)') が同じ文字列（例：「水」）になるようロケールを固定
Carbon::setLocale('ja'); //#1

$date = now()->toDateString(); // 2025-10-15
$clockIn = now();              // 2025-10-15 09:00:00

//Carbonはグローバル設定なので他のテストケースに影響しないようリセット
Carbon::setTestNow();
```

→ どの行で `now()` を呼んでも、完全に同じ時刻が返る。
→ テスト実行タイミングに依存しない。
→ したがって「date と clock_in が一致する」ことを安定的に検証できる。

---


## 1. Carbon::setLocale('ja');

### 必要になるケース

| ケース | 説明 | setLocale('ja') の必要性 |
|--------|------|----------------|
| `isoFormat('YYYY年M月D日(ddd)')` | `(ddd)` が曜日（例: Mon, Tue） | ✅ 必要 |
| `translatedFormat('m/d(D)')` | `(D)` が曜日（例: Mon, 火） | ✅ 必要 |
| `diffForHumans()` | “2 hours ago” → “2時間前” に変換 | ✅ 必要 |
| `format('Y-m-d H:i')` | 英数字のみの標準フォーマット | ❌ 不要 |
| `toDateString(), toTimeString()` | 言語非依存の数値表記 | ❌ 不要 |

---

### つまり

- **「英語表記を日本語にしたい」時 → 必要**  
  （曜日・相対時刻など、言語を含む出力）
- **「数字だけ出力したい」時 → 不要**