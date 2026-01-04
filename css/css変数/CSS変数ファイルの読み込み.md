---
title: variables.cssの読み込み
tags:
  - css変数
  - 呼び出し
---

## 説明
css変数のファイルvariables.cssをhtmlに読み込ませる方法とルール
point :  実際のスタイルよりもCSS変数を"先に"読み込む！

```css
<head>

    <meta charset="UTF-8">

    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title></title>

    {{-- CSSは上から順に読み込まれる = 同じセレクタ/プロパティを複数回指定すると後に読み込まれたほうが勝つ --}}

    {{-- リセットCSS（ress） を最初に読み込み、ブラウザごとの余白や見出しの差をなくす--}}

    <link rel="stylesheet" href="https://unpkg.com/ress/dist/ress.min.css" />

    {{-- 変数定義（variables.css） を次に読み込む --}}

    <link rel="stylesheet" href="{{ asset('css/variables.css') }}" />

    {{-- 実際のスタイル（register.css）を最後に読み込むことによってプロパティごとに上書きも可能 --}}

    <link rel="stylesheet" href="{{ asset('css/register.css') }}" />

</head>