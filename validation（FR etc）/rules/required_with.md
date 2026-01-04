---
tags:
  - validation
  - required_with
  - validation_rules
---

🧩 required_with: とは？

指定した別の項目が入力されている場合、この項目も必須にする
というルールです。

🔹 基本の形
'required_with:他のフィールド名'

🔸 例
'break_start' => ['required_with:break_end'],
'break_end'   => ['required_with:break_start'],


👉 意味はこう：

「もし break_end が入力されていたら、break_start も必須」
「もし break_start が入力されていたら、break_end も必須」

つまり「どちらか片方だけ入力」はNGになります。
両方空はOK（nullableがついていれば）。

💬 文章で説明すると

「休憩終了時刻が入力されている場合は、休憩開始時刻も入力してください」
「休憩開始時刻が入力されている場合は、休憩終了時刻も入力してください」


この required_with は「ペアで使う項目」にすごく向いてます。
つまり今回のような「開始」「終了」がセットの項目にはぴったり。