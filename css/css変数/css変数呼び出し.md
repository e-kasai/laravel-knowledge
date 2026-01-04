---
title: css変数呼び出し
tags:
  - css変数
  - 呼び出し
---

## 説明
定義したcss変数の呼び出し方

```css

/* シンプルなよびだし例 */
　--color-bg:         #f1ece7; 
  background-color: var(--color-bg);


/* clamp使用時の呼び出し例 */
  --fs-form__label-min: 1.5rem;
  --fs-form__label-fluid: 4vw;
  --fs-form__label-max: 2rem;
  

  font-size: clamp(
        var(--fs-header_ttl-min),
        var(--fs-header_ttl-fluid),
        var(--fs-header_ttl-max)
    );


