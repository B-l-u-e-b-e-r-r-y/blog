---
title: CSS 排版神器 Flexbox
date: 2020-02-15
tags: 
- Flexbox
- RWD
categories: 
- CSS
---

# CSS 排版神器 Flexbox

在 Flexbox 中，部分屬性只能用於父元素，部分只能用於子元素。
![](/images/flexbox/fathernchildren.jpg)
* **父元素屬性**
    * display
    * flex-direction
    * flex-wrap
    * justify-content
    * align-items
    * align-content
* **子元素屬性**
    * align-self
    * order
    * flex
        * flex-grow
        * flex-shrink
        * flex-basis
    

## 父元素屬性

### display

```css
display: flex | inline-flex;
```

要使用 Flexbox 必須先定義一個父元素容器，並於父元素定義 `display` 屬性為 `flex` 或 `inline-flex`。
* `flex`：Flexbox 的常用屬性，後方的元素會換行
* `inline-flex`：類似於 `inline-block` 加 `flex`，在後方的元素不會換行

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="css,result" data-user="Priscilla_Lin" data-slug-hash="LYVpaLd" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex vs inline-flex">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/LYVpaLd">
  flex vs inline-flex</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

---------------------------------------

### flex-direction
```css
flex-direction: row<default> | row-reverse | column | column-reverese;
```

`flex-direction` 可以指定元素的排列方式。
* `row`：預設值，橫向排列
* `column`：直向排列
* `row-reverse`：橫向反向排列
* `column-reverse`：直向反向排列

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="JjdYgWx" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex-direction">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/JjdYgWx">
  flex-direction</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### flex-wrap
```css
flex-wrap: nowrap<default> | wrap | wrap-reverse;
```

當多個子元素超過父元素的寬度時，`flex-wrap` 可以決定是否將子元素換行。
* `nowrap`：預設值，不換行，子元素會縮短以符合父元素的寬度
* `wrap`：換行，超出父元素寬度的子元素會換到下一行
* `wrap-reverse`：反向換行，也就是各行的順序會顛倒

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="zYGvgEm" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex-wrap">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/zYGvgEm">
  flex-wrap</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### justify-content
```css
justify-content: flex-start<default> | flex-end | center | space-between | space-around;
```

`justify-content` 決定水平對齊的位置。
* `flex-start`：預設值，靠左對齊
* `flex-end`：靠右對齊
* `center`：置中對齊
* `space-between`：平均分配，左右貼齊父元素
* `space-around`：平均分配，類似 margin 的概念
![](/images/flexbox/jc-space-around.jpg)
* `space-evenly`：平均分配，每個間距都是相同寬度
![](/images/flexbox/jc-space-evenly.jpg)

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="dyoGPwP" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="justify-content">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/dyoGPwP">
  justify-content</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### align-items
```css
align-items: flex-start | flex-end | center | stretch<default> | baseline;
```

`align-items` 決定垂直對齊的位置。
* `flex-start`：靠上方對齊
* `flex-end`：靠下方對齊
* `center`：置中對齊
* `stretch`：預設值，所有元素撐開與父元素同高
* `baseline`：依元素的基準線對齊
![](/images/flexbox/ac-baseline.jpg)

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="abOdKwb" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="align-items">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/abOdKwb">
  align-items</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### align-content
```css
align-content: flex-start | flex-end | center | stretch<default> | space-between | space-around;
```

`align-content` 針對多行的元素進行排序。
* `flex-start`：靠上方對齊
* `flex-end`：靠下方對齊
* `center`：置中對齊
* `stretch`：預設值，所有元素撐開與父元素同高
* `space-between`：平均分配，上下貼齊父元素
* `space-around`：平均分配，類似 margin 概念

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="MWwKXvx" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="align-content">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/MWwKXvx">
  align-content</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

## 子元素屬性

### align-self
```css
align-self: flex-start | flex-end | center | stretch | baseline;
```

`align-self` 針對各別子元素進行排序，不受其他影響。
* `flex-start`：靠上方對齊
* `flex-end`：靠下方對齊
* `center`：置中對齊
* `stretch`：子元素撐開與父元素同高
* `baseline`：依元素的基準線對齊

這邊我直接拿上面的 `align-items` 的範例來改，可以看到 2 號橘色方塊被我設定為 `align-center: center`，所以不管怎麼排序，橘色方塊都會垂直置中。

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="html,result" data-user="Priscilla_Lin" data-slug-hash="ExjPjYB" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="align-self">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/ExjPjYB">
  align-self</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### order
```css
order: <number>;
```

`order` 可以決定子元素的排序順序，只能給定數字。

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="css,result" data-user="Priscilla_Lin" data-slug-hash="NWqxOby" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="order">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/NWqxOby">
  order</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

### flex
```css
flex: <flex-grow> <flex-shrink> <flex-basis>;
```

`flex` 是由三個值組成，如果只給定一個值，則是給定 `flex-grow`。

#### flex-grow
```css
flex-grow: <number>;
```

當子元素的總寬度**不超過**父元素時，`flex-grow` 會分配剩餘的空間給特定子元素。

`flex-grow` 的預設值為 0，設定 1 以上的值會進行彈性變化。

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="css,result" data-user="Priscilla_Lin" data-slug-hash="poJgMoN" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex-grow">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/poJgMoN">
  flex-grow</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

#### flex-shrink
```css
flex-shrink: <number>;
```

當子元素的總寬度**超過**父元素時，`flex-shrink` 會將特定子元素的寬度依比例縮減。

`flex-shrink` 的預設值為 1，設為 0 的話不會進行彈性變化。

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="css,result" data-user="Priscilla_Lin" data-slug-hash="zYGqOvb" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex-shrink">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/zYGqOvb">
  flex-shrink</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>

---------------------------------------

#### flex-basis
```css
flex-basis: <number>;
```

`flex-basis` 會指定子元素在主軸 (main axis) 上的初始大小。

`flex-direction: row` 為更改子元素寬度，反之 `flex-direction: column` 則更改子元素高度。

`flex-basis` 的預設值為 0，也可以設定為 auto，表示以子元素自己的大小為基準。

範例程式碼：
<p class="codepen" data-height="265" data-theme-id="default" data-default-tab="css,result" data-user="Priscilla_Lin" data-slug-hash="BaNKBjZ" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="flex-basis">
  <span>See the Pen <a href="https://codepen.io/Priscilla_Lin/pen/BaNKBjZ">
  flex-basis</a> by Priscilla Lin (<a href="https://codepen.io/Priscilla_Lin">@Priscilla_Lin</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>