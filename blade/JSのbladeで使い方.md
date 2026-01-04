
---

### 使い方パターンまとめ

１ bladeに直書き　　　　　　　  @push/@stuckでよみこみ （.jsファイルすら作らない）
２ 共通で使う場合　　　　　　　 app.jsに直接書く
３ 特定ページで使うだけ　　　　.jsファイルを作成してapp.jsでimport or app.blade.phpで@push/@stack

---

#### 1. Bladeに直書き
* 基本的には </body> の直前 に読み込む
* HTMLが先に全部レンダリングされてからJSを実行した方が「要素がまだない」エラーを防げる
* 小さい処理・実験用・そのページだけの一時的な動き。


```html
{{-- layouts/app.blade.php で読み込む--}}
<html>
  <head>
    @stack("styles")
  </head>
  <body>
    @yield("content")

    @stack("scripts") //ここに@pushが入る
  </body>
</html>
```

```html
//各viewでJSを書く
@push("scripts")
    <script>
        document.addEventListener("DOMContentLoaded", () => {
            console.log("準備できたわよ！");
        });
    </script>
@endpush
```

---

#### 2. 全体で使う場合（app.js に直接書く）

* ヘッダーや検索バーなど、全ページに必要なUI処理。
* まとめて resources/js/app.js に直書きすればOK。
* Viteがビルドして全ページに読み込まれる。

---

#### 3. 特定ページで使うだけの場合

方法は2パターン：

** A: xxx.js を作って app.js から import **
→ 管理しやすくなる（「購入ページ専用処理は purchase.js」にまとめるなど）

** B: app.blade.php で @push/@stack して直接読み込む **
→ そのページだけで完結させたいとき
→ 1との違いは " .jsファイルを作るか作らないか "

---
