---
title: CSS Grid 格線佈局
comments: true
banner_img: /images/grid_banner.png
index_img: /images/grid_banner.png
date: 2021-07-04 18:01:00
tags: 
- Grid
- CSS
categories: 
- CSS
---
# 前言

[Flexbox](https://b-l-u-e-b-e-r-r-y.github.io/post/Flexbox/) 的出現讓很多人捨棄了舊有的排版方式，大多數的時候我也還是使用 Flexbox，但最近遇到的需求版型比較複雜，於是開始學習 Grid。

我認為 Grid 很適合用在 Dashboard 的排版，比如：
![](/images/grid/grid-dashboard.png)

不是說 Flexbox 無法做到這個版型，而是用 Grid 來排真的比較快又簡單很多。

## Google Chrome 的格線預覽

開始前先來介紹實用的 Google Chrome 小工具。有一個版型長這樣：

![](/images/grid/grid-normal.jpeg)

藉由 F12 開發者工具右上角的功能，可以看到原本的格線佈局：

![](/images/grid/grid-brower.png)

# 開始使用

先讓包在最外層的父元素變成 Grid Container：

```css
.wrapper {
    display: grid;
}
```

## 設定 Grid Container

要設定 Grid Container，最基本會用到 `grid-template-rows` / `grid-template-columns` 這兩個屬性。

![grid-template-rows 是縱軸，grid-template-columns 則是橫軸](/images/grid/grid-01.png)

### 明確定義寬高

簡單來說就是使用 px、%、vw 或 vh 去設定寬高：

```css
.wrapper {
    display: grid;
    grid-template-columns: 200px 200px 200px;
}
```

來切個常見的 layout。

**HTML**
```html
<div class="wrapper">
    <div>menu</div>
    <div>nav</div>
    <div>sidebar</div>
    <div>content</div>
</div>
```

**CSS**
```css
.wrapper {
    display: grid;
    grid-template-rows: 60px 600px;
    grid-template-columns: 200px 1000px;
}

.wrapper div {
    border: solid 1px;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="YzVyaeX" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/YzVyaeX">
  Grid 01</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

### 用等分決定寬高

這時候就是使用只有在 Grid 出現的格線單位：`fr`。1fr 是一等分的意思，2fr 就是兩等分，以此類推。

```css
.wrapper {
    display: grid;
    grid-template-rows: 2fr 1fr 1fr;
    grid-template-columns: 2fr 1fr 1fr;
}
```

也可以和自定義的數值混用，這樣的話就會扣掉固定數值，剩下的寬(高)再平分。

```css
.wrapper {
    display: grid;
    grid-template-rows: 2fr 1fr;
    grid-template-columns: 200px 1fr 1fr;
}
```

設定成 Grid Container 後，寬度預設是滿版，所以 columns 會以 100 vw 下去切成好幾等分。

但 rows 就不同了，它會把子元素的高度當成依據，去計算 1fr 的高度是多少。如果同一行中有不同的高度，則以高度最大的那個子元素為準。

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="gOWaedg" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/gOWaedg">
  Grid 02</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### Repeat

`Repeat(格數, 格寬/高)`

如果 Row 或 Column 的寬度是相同的，而且需要好幾格，這時候就可以用 `Repeat`：

```css
.wrapper {
    display: grid;
    grid-template-rows: Repeat(10, 50px);
    grid-template-columns: Repeat(10, 1fr);
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="rNjxRRX" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/rNjxRRX">
  Grid repeat</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### minmax

`minmax(最小值, 最大值)`

`minmax` 可以指定最小值和最大值，在設計響應式頁面很方便：

```css
.wrapper {
    display: grid;
    grid-template-rows: minmax(50px, 100px);
    grid-template-columns: minmax(50px, 100px);
}
```

以上面的例子來看，一般 PC 的情況下 columns 會呈現 100px，隨著頁面寬度變化慢慢縮小，如果擠到不能再擠了，就會維持最小高度 50px。rows 也是一樣的原理。

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="zYwOjeL" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/zYwOjeL">
  Grid minmax</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## 客製化區塊

要做到客製化區塊，可以靠這兩種設定方式做到：

### grid-template-areas

`grid-template-areas` 可以單獨定義每一格，但有幾點必須注意：

- 區塊如果要合併，必須要連續，像ㄇ形或L型是不行的
- 定義完後，子元素的 class 必須加入 grid-area 指定區塊
- 使用 `.` 可以留空區塊

**HTML**

```html
<div class="wrapper">
    <div class="nav">nav</div>
    <div class="sidebar">sidebar</div>
    <div class="content-01">content-01</div>
    <div class="content-02">content-02</div>
    <div class="content-03">content-03</div>
</div>
```

**CSS**

```css
.wrapper {
    display: grid;
    grid-template-rows: 60px 300px 300px;
    grid-template-columns: 200px 500px 500px;
    grid-template-areas: 
    "nav nav nav"
    "sidebar content-01 content-02"
    "sidebar content-03 content-04";

    /* (x) 倒L型 nav
        * grid-template-areas: 
    *   "nav nav nav"
    *   "nav content-01 content-02"
    *   "nav content-03 content-04";
    */
}

.wrapper div {
    border: solid 1px;
}

.nav {
    grid-area: nav;
}

.sidebar {
    grid-area: sidebar;
}

.content-01 {
    grid-area: content-01;
}

.content-02 {
    grid-area: content-02;
}

.content-03 {
    grid-area: content-03;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="PomPRxm" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/PomPRxm">
  Grid grid-template-areas</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### 各個 Class 分別指定

這邊也分成兩種方式：

- **grid-[row/column]-start / grid-[row/column]-end**

```css
grid-row-start: 1;
grid-row-end: 2;
grid-column-start: 2;
grid-column-end: 3;
```

- **grid-[row/column]: start / end**

```css
grid-row: 1 / 2;
grid-column: 2 / 3;
```

數字的部分是這樣數過去，由左而右，由上到下，從 1 開始數：

![](/images/grid/grid-02.png)

這邊有兩個特殊用法：

- **span**：跨越幾個區塊
```css
grid-row: 1 / span 3;
```

- **auto**：設定 auto 通常為 start 值 +1
```css
grid-row: 1 / auto;
```

**CSS**
註解和未註解的部分是同樣效果不同寫法（HTML 沿用上個範例）

```css
.wrapper {
    display: grid;
    grid-template-rows: 60px 300px 300px;
    grid-template-columns: 200px 500px 500px;
}

.wrapper div {
    border: solid 1px;
}

/* -------------------- */

/* .nav {
    grid-row-start: 1;
    grid-row-end: 2;
    grid-column-start: 1;
    grid-column-end: span 3;
} */

.nav {
    grid-row: 1 / 2;
    grid-column: 1 / span 3;
}

/* -------------------- */

/* .sidebar {
    grid-row-start: 2;
    grid-row-end: 4;
    grid-column-start: 1;
    grid-column-end: 2;
} */

.sidebar {
    grid-row: 2 / 4;
    grid-column: 1 / 2;
}

/* -------------------- */

/* .content-01 {
    grid-row-start: 2;
    grid-row-end: 3;
    grid-column-start: 2;
    grid-column-end: 3;
} */

.content-01 {
    grid-row: 2 / 3;
    grid-column: 2 / 3;
}

/* -------------------- */

/* .content-02 {
    grid-row-start: 2;
    grid-row-end: 3;
    grid-column-start: 3;
    grid-column-end: 4;
} */

.content-02 {
    grid-row: 2 / 3;
    grid-column: 3 / 4;
}

/* -------------------- */

/* .content-03 {
    grid-row-start: 3;
    grid-row-end: 4;
    grid-column-start: 3;
    grid-column-end: 4;
} */

.content-03 {
    grid-row: 3 / 4;
    grid-column: 3 / 4;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="rNmOKLj" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/rNmOKLj">
  Grid assign block in child</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## grid-auto-rows / grid-auto-columns

如果只是想要簡單設定 rows 或 columns，可以用 `grid-auto-rows` 或 `grid-auto-columns`。

### grid-auto-rows

```css
.wrapper {
    display: grid;
    width: 50vw;
    grid-template-columns: 1fr 1fr 1fr;
    grid-auto-rows: 100px;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="VwbvBvJ" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/VwbvBvJ">
  Grid auto rows</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

### grid-auto-columns

```css
.wrapper {
    display: grid;
    height: 500px;
    grid-auto-columns: 200px;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="QWpPJyE" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/QWpPJyE">
  Grid auto columns</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

## gap

gap 各區塊之間的間距，可以一起設定，也可以 rows 和 columns 分開設定。

### 一起設定 gap

```css
.wrapper {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    grid-template-rows: 1fr 1fr 1fr;
    grid-gap: 10px;
}
```

### 分開設定 gap

```css
.wrapper {
    display: grid;
    width: 50vw;
    grid-template-columns: 1fr 1fr 1fr;
    grid-auto-rows: 100px;
    grid-row-gap: 10px;
    grid-column-gap: 0px;
}
```

<p class="codepen" data-height="300" data-default-tab="html,result" data-slug-hash="VwbvBKo" data-user="Priscilla_Lin" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/VwbvBKo">
  Grid gap</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

# Grid 對不同瀏覽器的支援

Grid 目前的支援不像 Flexbox 那麼好，不過主流瀏覽器幾乎都有支援。

[CSS Grid Layout (level 1) | Can I use... Support tables for HTML5, CSS3, etc](https://caniuse.com/css-grid)
