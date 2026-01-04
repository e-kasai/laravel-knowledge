---
title: CSS変数再定義
tags:
---

## 説明
ページによってCSS設定を変えたい variables.cssで定義している変数をblade.phpごとに上書きしてそのページにだけ特定のCSSを適用する方法

```css
/* 例:variables.css で --btn-bg: brown; と定義して、register.css では別の色に変えたければ
同じセレクタ（例: :root か特定のスコープ内）で再定義すれば上書きされる*/


/* 例: register.bladeのボタンだけblueにしたい = "register.css内"でrootを再定義 */
:root {
  --btn-bg: darkblue;
}

/*register.cssを読み込んだbladeでのみ↑の設定が適用となる*/