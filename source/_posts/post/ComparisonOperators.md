---
title: 比較運算子
date: 2020-02-29
comments: true
categories: 
- Javascript
---

# 比較運算子

:::note
請先參閱：[運算子優先序](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/Operator_Precedence) 
:::

先來看一段程式碼：

```js
console.log(3 < 2 < 1);   // 結果是 true
```

以人類的邏輯來說，這絕對是錯的，但為什麼 JavaScript 會判定為 true 呢？

小於運算子的相依性是**由左至右**，程式會優先進行 `3 < 2` 這部分的判定。於是會變成：

```js
console.log(false < 1);
```

但是 `false` 和 `1` 並不是同一個型別，所以 JavaScript 會進行**強制型轉**。

如果你輸入下面這行程式碼，可以知道 false 轉成數字後的值：

```js
Number(false);   // 結果是 0
```

如此一來就變成了：

```js
console.log(0 < 1);
```

0 < 1，無庸置疑的是 `true`。

其他例子：

```js
Number(undefined);  // 結果是 NaN（Not a Number），無法型轉成數字
Number(null);       // 結果是 0
Number(!null);      // 結果是 1
```

---------------------------------------

:::note
請先參閱：[等號的比較](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)
:::

再來是 `==` 和 `===` 的區別。

為減少錯誤，建議從現在開始大部分時間都使用 `===`，除非故意要進行強制型轉。

以下例子使用 `==`。

```js
if (false == 0) {
    console.log(Equal.);
} else {
    console.log(Not equal.);
}
```

結果會是 Equal，因為 JavaScript 強制把 false 型轉成數字，故 `0 == 0` 結果是 true。

以下例子使用 `===`。

```js
if (false === 0) {
    console.log(Equal.);
} else {
    console.log(Not equal.);
}
```

結果會是 Not equal，**使用 === JavaScript 就不會進行強制型轉**，在兩個值是不同型態的情況下，結果會是 false。
