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

# 介紹

如果想要改變 this 指向的對象，可以透過 bind、call、apply 這三個 method 辦到。

```js
const person = {
   userName: 'Blueberry'
}
```

接著 create 一個函式來呼叫 person 的 userName：
```js
function callName() {
   console.log('Hello ' + this.userName);
}
```

呼叫 callName：
```js
callName();

/* Output:
   Hello undefined */
```

因為 callName() 函式中的 this 指向的是 global object（也就是 Window 物件），所以這時候我們希望把 callName() 的 this 指向 person 這個對象，可以使用以下方法：

## bind
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
*function.bind(thisArg[, arg1[, arg2[, ...]]])*

bind 和其他兩個方法（call、apply）的不同有兩點：
1. bind 是創造一個函式物件的拷貝，不會執行函式，因此 bind 之後還要再另外寫執行函式的動作；而 call、apply 是直接執行函式。
2. bind 後面傳入的參數值**會設定為拷貝函式的永久參數值**，之後執行拷貝函式時，無論怎麼給予參數都沒有用；而 call、apply 則是單純給予參數，像一般呼叫函式那樣。

我們先單純就第一點來說明，示範最基本的 bind 怎麼寫。

第一種寫法：
```js
callName.bind(person)();

// 也等於
// const callFunction = callName.bind(person);  // 創造函式物件的拷貝
// callFunction();  // 執行函式

/* Output:
   Hello Blueberry */
```

第二種寫法，直接寫在函式表示式後面也可以：
```js
const callName = function() {
   console.log('Hello ' + this.userName);
}.bind(person);

callName();

/* Output:
   Hello Blueberry */
```

接下來針對第二點來說明。先來改寫一下 callName 函式，加入兩個 arguments。
```js
function callName(age, interest) {
   console.log('Hello ' + this.userName);
   console.log('Your age is ' + age);
   console.log('Your interest is ' + interest);
}
```

接著我們除了要用 bind 將 this 指向 person 之外，還要給予 age 和 interest 這兩個參數。

這邊有兩種做法：單純給定參數，或綁定永久參數值。

### 單純給定參數

要單純給定參數的話，像一般執行函式那樣，在執行函式時再給予參數就可以了。

```js
const callFunction = callName.bind(person);
callFunction(24, 'Reading books');

/* Output:
   Hello Blueberry
   Your age is 24
   Your interest is Reading books */
```

### 綁定永久參數值

如果將參數放在 bind 中，這個函式拷貝物件的參數值就會永遠被固定住。

```js
const callFunction = callName.bind(person, 24, 'Reading books');
callFunction(30, 'Playing games');  // 這邊無論再怎麼給參數都沒用

/* Output:
   Hello Blueberry
   Your age is 24
   Your interest is Reading books */
```

## call
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
*function.call(this, arg1, arg2..., argn)*

call 和 bind 不同，它會直接執行函式，後面給的參數也不會被固定住。

```js
callName.call(person, 24, 'Reading books');

/* Output:
   Hello Blueberry
   Your age is 24
   Your interest is Reading books */
```

## apply
[MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
*function.apply(this, [arg1, arg2..., argn])*

apply 的寫法跟 call 很相近，與 call 不同的是，後面的參數需要使用陣列傳遞，適合搭配 arguments 運用在算數的函式。

```js
callName.apply(person, [24, 'Reading books']);

/* Output:
   Hello Blueberry
   Your age is 24
   Your interest is Reading books */
```

# 使用情境

## function borrowing

function borrowing 就是借別人函式中的方法來用的意思，下面示範 somebody 借用 person 的 getUserName 方法。

```js
const person = {
   userName: 'Blueberry',
   getUserName: function() {
      console.log(this.userName);
   }
}

const somebody = {
   userName: 'Fan'
}

person.getUserName.call(somebody);  // Fan

// or
// person.getUserName.apply(somebody);
// or
// const newUser = person.getUserName.bind(somebody);
// newUser();
```

## function currying

function currying 的定義為**建立一個函式的拷貝，並設定預設的參數**，這在數學運算下很有用。下面我們就利用 bind 的特性來完成 function currying。

```js
const mutiply = function (a, b) {
   return a * b;
}

const mutiplyByTwo = mutiply.bind(this, 2);
console.log(mutiplyByTwo(4));  // 8
```

上面我們建立了一個函式 `mutiply(a, b)`，並用 bind 建立函式物件拷貝 `mutiplyByTwo()`。

`mutiply.bind(this, 2)` 這邊的 this 並不重要，因為函式裡沒有使用到 this。而後面的 2 則是永久綁定了參數 a。

為了讓程式碼比較好理解，這邊將上面那段程式碼拆解，它也等於：

```js
const mutiply = function (b) {
   const a = 2;
   return a * b;
}

const mutiplyByTwo = mutiply.bind(this);
console.log(mutiplyByTwo(4));  // 8
```

---------------------------------------

參考資料：
[《JavaScript 全攻略：克服 JS 的奇怪部分》](https://www.udemy.com/course/javascriptjs/)