
---

#### やりたいこと

* 例：支払い方法選択するとリロードしなくても選んだ支払い方法に表示が切り替わる
---

#### code

```html
{{-- 支払い方法セレクトボックス --}}
            <div>
                <label for="payment_method"><span>支払い方法</span></label>
                <br />

                <select name="payment_method" id="payment_method" required>
                    <option value="" disabled hidden {{ old("payment_method") ? "" : "selected" }}>選択してください</option>
                    <option value="1" {{ old("payment_method") === "1" ? "selected" : "" }}>コンビニ払い</option>
                    <option value="2" {{ old("payment_method") === "2" ? "selected" : "" }}>カード支払い</option>
                </select>
                @error("payment_method")
                    <div>{{ $message }}</div>
                @enderror
            </div>
```

```html
{{-- 支払い方法のプレビュー --}}
            @php
                // ラベル表
                $paymentLabels = [
                    0 => "選択してください",
                    1 => "コンビニ払い",
                    2 => "カード支払い",
                ];
            @endphp
            <p id="payment_preview">
                {{ old("payment_method") ? $paymentLabels[(int) old("payment_method")] ?? "未選択" : "未選択" }}
            </p>
```

```js
// bladeの一番下（@endsectionの下）に追加
@push("scripts")
    <script>
	    //document = 今表示しているHTMLドキュメント
		//addEventListener('DOMContentLoaded', …)「HTMLの読み込みがぜんぶ終わったタイミングで、この中の関数を実行
        document.addEventListener('DOMContentLoaded', () => {
            const select = document.getElementById('payment_method');    //HTMLから id="payment_method" のセレクトタグを探し変数selectに代入
            const preview = document.getElementById('payment_preview'); //<p id="payment_preview"> を探して変数に代入

            if (!select || !preview) return; // セレクトなし or プレビューなし →この先の処理は全部やめる

            // value→ラベルの対応（Blade側と一致）
            const labels = {
                1: 'コンビニ払い',
                2: 'カード支払い',
            };

            const update = () => {
                const val = select.value; //今のセレクトの値を取る
                preview.textContent = labels[val] ?? '未選択'; //その値に対応するラベルを表示
            };

            // 初期表示（old値反映）
            update();

            // addEventListener は「この要素にイベントが起きたら関数を呼ぶ」という命令
            // 'change' イベントは「セレクトで選択肢が切り替わったとき」に発生
            select.addEventListener('change', update);
        });
    </script>
@endpush
```


---

#### 用語まとめ

* DOM (document Object Model)
htmlをJSが理解できるようにツリー構造にしたもの
![[Pasted image 20250909151249.png]]

* labels[val]
JSのオブジェクトで、値に対応する文字列を取り出す
（例：labels[1] → "コンビニ払い"）

* !xx 
もしxxがなければ

* getElementById
対象のHTML部品を変数に取り込む