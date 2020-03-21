---
title: 使用 bind、call、apply 改變 this 指向的對象
comments: true
banner_img: /images/js_banner.jpeg
index_img: /images/js_banner.jpeg
date: 2019-12-30 00:00:00
tags: Javascript
categories: 
- Javascript
---

如果想要改變 this 指向的對象，可以透過 bind、call、apply 這三個 method 辦到。

```js
const obj = {
    userName: 'Blueberry'
}
```

接著 create 一個函式來呼叫 obj 的 userName：
```js
function callName() {
    console.log('hello ' + this.userName);
}
```

呼叫 callName：
```js
callName();

/* Output:
   hello undefined */
```

因為 callName() 函式中的 this 指向的是 global object（也就是 Window 物件），
所以這時候我們希望把 callName() 的 this 指向 obj 這個對象，使用以下方法：


## 1. bind
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
*function.bind(thisArg[, arg1[, arg2[, ...]]])*

第一種寫法：
```js
callName.bind(obj)();

// 意即
// var callFunction = callName.bind(obj);
// callFunction();

/* Output:
   hello Blueberry */
```

第二種寫法，直接寫在函式表示式後面也可以：
```js
var callName = function() {
    console.log('hello ' + this.userName);
}.bind(obj);

callName();

/* Output:
   hello Blueberry */
```

## 2. call
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
*function.call(this, arg1, arg2..., argn)*

call 除了可以綁定對象給 this 之外，後面還可以直接放入參數。
我們先來改寫一下 callName()：
```js
function callName(age) {
    console.log('hello ' + this.userName);
    console.log('your age is ' + age);
}
```

多了 age 這個參數可代入，接著來示範 call 怎麼寫：
```js
callName.call(obj, 20);

/* Output:
   hello Blueberry
   your age is 20 */
```

## 3. apply
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
*function.apply(this, [arg1, arg2..., argn])*

apply 的寫法跟 call 很相近，與 call 不同的是，
後面的參數需要使用陣列傳遞，適合搭配 arguments 運用在算數的函式。

```js
callName.apply(obj, [20]);

/* Output:
   hello Blueberry
   your age is 20 */
```