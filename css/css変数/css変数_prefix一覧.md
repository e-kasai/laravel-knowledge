---
title: css変数のprefix一覧
tags:
  - css変数
---
## 説明
よく使うCSS変数のテンプレート命名の参考に


### code
```css
:root {
  /* カラー */
  --color-primary:    #3498db;
  --color-secondary:  #6c63ff;
  --color-text:       #333333;
  --color-bg:         #f1ece7; 
   /* コンテンツを載せる白いカードやモーダルなど */
  --color-surface: white;
   /* surface上の表示テキストの色 */
  --color-on-surface: #333333;
  --color-border: #e0dfde;
  --color-border-light: #c5b9b0;
  --color-focus: #6c63ff;
  
   
 /* ボタン共通変数 */
  :root {
  --clr-btn-bg:        brown;
  --clr-btn-txt:       white;
  --bd-btn-width:      1px;
  --bd-btn-style:      solid;
  --bd-btn-color:      brown;
  --btn-padding-vert:  0.6rem;
  --btn-padding-horiz: 3rem;
  --btn-margin:        2rem auto;
  --btn-radius:        0.4rem;
  --btn-transition:    200ms ease-in-out;
}

/* ボタン実装 */
.register-form__button {
  display: block;
  background-color: var(--clr-btn-bg);
  color:            var(--clr-btn-txt);
  border:           var(--bd-btn-width) var(--bd-btn-style) var(--bd-btn-color);
  padding:          var(--btn-padding-vert) var(--btn-padding-horiz);
  margin:           var(--btn-margin);
  border-radius:    var(--btn-radius);
  transition:       var(--btn-transition);
  cursor:           pointer;
}
 
 
  /* ボーダー */
  --border:  1px solid #C5B9B0;
  --bd-radius:      0.8rem;
  --bd-width:       0.1rem;
  
  /* フォントサイズ（ヘッダータイトル用） */
  --fs-header_ttl-min: 2rem;
  --fs-header_ttl-fluid: 4vw;
  --fs-header_ttl-max: 3rem;
  /* フォントサイズ（メインコンテンツタイトル用） */
  --fs-main_ttl-min: 1.8rem;
  --fs-main_ttl-fluid: 4vw;
  --fs-main_ttl-max: 2.5rem;
  /* フォントサイズ（フォームラベル用） */
  --fs-form__label-min: 1.5rem;
  --fs-form__label-fluid: 4vw;
  --fs-form__label-max: 2rem;
  
  /* clamp使用時のよびだし例 */
  font-size: clamp(
        var(--fs-header_ttl-min),
        var(--fs-header_ttl-fluid),
        var(--fs-header_ttl-max)
    );
  
  
  /* フォント(ヘッダータイトル,メインコンテンツタイトル用）*/
  --ff-title: serif;
  /* 見出し用フォント */
  --ff-heading: serif;
  /* 本文用フォント */
  --ff-body: "Helvetica Neue", sans-serif;


  /* フォントファミリ & サイズ */
  --ff-heading:     'Helvetica Neue', sans-serif;
  --ff-body:        serif;
  --fs-base:        1.6rem;
  --fs-lg:          2.4rem;
  --fs-sm:          1.2rem;

  /* 間隔 */
  --sp-xxs:         0.4rem;
  --sp-sm:          0.8rem;
  --sp-md:          1.6rem;
  --sp-lg:          2.4rem;

  /* ブレークポイント */
  --bp-sm:          576px;
  --bp-md:          768px;
  --bp-lg:          992px;

 
  /* シャドウ */
  --bs-light:       0 2px 4px rgba(0,0,0,0.1);
  --bs-heavy:       0 4px 8px rgba(0,0,0,0.2);

  /* トランジション */
  --tr-fast:        150ms ease-in-out;
  --tr-default:     300ms ease-in-out;

  /* Z-Index */
  --z-dropdown:     1000;
  --z-modal:        2000;

  /* 不透明度 */
  --op-disabled:    0.5;
  --op-hover:       0.8;
}
