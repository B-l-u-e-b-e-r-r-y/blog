---
title: Functional Programming 初級入門
comments: true
banner_img: /images/js_banner.jpeg
index_img: /images/js_banner.jpeg
date: 2020-05-02 00:00:00
tags: 
- w3HexSchool
- Javascript
- Functional Programming
categories: 
- Javascript
---

# 前言

最近打算把 Udemy 的這堂[《JavaScript 全攻略：克服 JS 的奇怪部分》](https://www.udemy.com/course/javascriptjs/)補完，才發現裡面有提到 Functional Programming 的觀念，之前就一直想學這塊，今天剛好有這個機會可以入門。

在堅持鐵人賽的這幾週，漸漸也養成了在週六學習新知的習慣，但也發現一天的時間要包含學習和寫文章有點不太夠，之後希望能把時間管理慢慢做好，多騰出一些平日的時間來學習，邁向一週寫兩篇文的目標前進。

# 什麼是 Functional Programming

Functional Programming（以下簡稱 FP）是一種編程範例（Programming paradigm），可以理解為是一種程式設計風格，只有在擁有一級函式（First-class Function）的程式語言中才能實作。

# 實作

現在我們有 `arr1` 和 `arr2` 兩個陣列。我希望 `arr2` 陣列內的每個數字是 `arr1` 的數字 * 2。

一般我們可能會這樣做：

```js
const arr1 = [1, 2, 3];
const arr2 = [];

for (let i = 0; i < arr1.length; i++) {
    arr2.push(arr1[i] * 2);
}

console.log(arr2);  // [2, 4, 6]
```

為了符合 FP 思維，接下來會將程式碼拆分成可複用的函式。

```js
const mapForEach = (arr, fn) => {
    const output = [];
    for (let i = 0; i < arr.length; i++) {
        output.push(fn(arr[i]));
    }
    return output;
}

const arr1 = [1, 2, 3];
const arr2 = mapForEach(arr1, item => item * 2);

console.log(arr2);  // [2, 4, 6]
```

但是這樣還不夠，我希望 `mapForEach(arr1, item => item * 2)` 的部分可以更彈性。

下面將 `item * 2` 的部分，拆分成可以自訂數字的 `pastNumber()` 函式：

```js
const mapForEach = (arr, fn) => {
    const output = [];
    for (let i = 0; i < arr.length; i++) {
        output.push(fn(arr[i]));
    }
    return output;
}

const pastNumber = (number, item) => {
    return item * number;
}

const arr1 = [1, 2, 3];
const arr2 = mapForEach(arr1, pastNumber.bind(this, 2));

console.log(arr2);  // [2, 4, 6]
```

這一段程式碼我自己看了蠻長一段時間才懂，一直不懂這邊明明只給了 `number` 的值，`item` 的值是怎麼取到的。後來想到之前寫的[拆解 bind 程式碼](https://b-l-u-e-b-e-r-r-y.github.io/post/BindCallApply/#function-currying)，也用同樣的方式拆解，才理解這段的意思。
```js
const pastNumber = (number, item) => {
    return item * number;
}

const arr2 = mapForEach(arr1, pastNumber.bind(this, 2));

// 等於
const pastNumber = (item) => {
    const number = 2;
    return item * number;
}

const arr2 = mapForEach(arr1, pastNumber);
```

但是每次都要寫 bind 實在很麻煩，有沒有更簡潔的寫法？

於是把這段 bind 也裝成一個函式，寫法簡潔多了。

```js
const mapForEach = (arr, fn) => {
    const output = [];
    for (let i = 0; i < arr.length; i++) {
        output.push(fn(arr[i]));
    }
    return output;
}

const pastNumberSimplify = (number) => {
    return function (_number, item) {
        return item * _number;
    }.bind(this, number);
}

// 其實就等於
// const pastNumber = (number, item) => {
//     return item * number;
// }
// const pastNumberSimplify = (number) => {
//     return pastNumber.bind(this, number);
// }

const arr4 = mapForEach(arr1, pastNumberSimplify(2));  // [2, 4, 6]
```

> 這篇文變成好像是單純在解讀程式碼 XD，第一次接觸 FP，還寫不出什麼很深入的東西。內容都是跟著課程實作的，可以跟著寫看看，一起來感受 FP 的美好。

---------------------------------------

參考資料：
[《JavaScript 全攻略：克服 JS 的奇怪部分》](https://www.udemy.com/course/javascriptjs/)
