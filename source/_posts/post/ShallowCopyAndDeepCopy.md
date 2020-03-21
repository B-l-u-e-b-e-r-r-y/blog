---
title: Javascript 的淺拷貝與深拷貝
comments: true
banner_img: /images/js_banner.jpeg
index_img: /images/js_banner.jpeg
date: 2020-03-21 18:00:00
tags: 
- Javascript
- w3HexSchool
categories: 
- Javascript
---
# 前言
在寫 JS 的過程中，常常會遇到需要拷貝物件或陣列的情況。
然而之後修改拷貝的內容時，就會發現原本的物件或陣列怎麼也一起改了？
好比以下的程式碼：
```js
const person = {
    name: 'Blueberry',
    age: '24',
    interest: '閱讀'
};

const copy = person;
copy.interest = '泡咖啡';
console.log('copy: ', copy);
console.log('person: ', person);

/**
 * [Output]
 *
 * copy: Object {
 *     name: 'Blueberry',
 *     age: '24',
 *     interest: '泡咖啡'
 * }
 * person: Object {
 *     name: 'Blueberry',
 *     age: '24',
 *     interest: '泡咖啡'  // 疑等等？怎麼不是'閱讀'了？
 * }
 */
```
為什麼我修改的是 `copy` 的 interest，卻連 `person` 的值也一起改了？
這就跟 __by value__ 與 __by reference__ 有關了。

# by value VS by reference
兩者的不同在於**記憶體的位置**，如果是 by value，複製時記憶體位置會是**不同**的，
但如果是 by reference，複製時記憶體位置會是**相同**的，下面就來詳細介紹與舉例。

## by value
```js
const a = 1;
const b = a;

a = 2;
console.log(a, b);  // 1, 2
```
根據上面的範例，`a` 在記憶體中位於 `0x001` 位置，複製 a 的 `b` 在記憶體中位於 `0x002` 位置。
**兩者位於不同的記憶體位置，所以在後續修改時，不會影響到對方。**
只要是**純值（數字、布林或字串）**，就是 by value。

## by reference
```js
const a = { name: 'Blueberry' };
const b = a;

a.name = 'Strawberry';
console.log('a: ', a);
console.log('b: ', b);

/**
 * [Output]
 *
 * a: Object {
 *     name: "Strawberry"
 * }
 * b: Object {
 *     name: "Strawberry"
 * }
 */ 
```
根據上面的範例，a 在記憶體中位於 `0x001` 位置，複製 a 的 b 在記憶體中也位於 `0x001` 位置。沒有新的物件產生，也沒有物件的拷貝被創造。
**兩者位於相同的記憶體位置，可以說 a 其實就是 b，只是換個名字而已，所以在後續修改時，會影響到對方。**
只要是**物件（陣列、物件或函式）**，就是 by reference。
如果不希望修改時會影響到對方，就必須得使用**淺拷貝**或**深拷貝**去複製。

# 淺拷貝 VS 深拷貝
兩者的差異在於，**淺拷貝**只能複製第一層，無法深層複製第二層、第三層...等等，如果更改了第二層以後的內容，一樣會影響到原本的物件，因為第二層以後的記憶體位置與原本物件的記憶體位置還是相同的。**但如果需要拷貝的物件只有一層，用淺拷貝還是可以的。**
而**深拷貝**就不只了，它能深層複製第一層乃至最內層。**不論修改的是哪一層，都不會被影響，因為它的每一層與原物件都是不同的記憶體位置。**

下面就來介紹淺拷貝與深拷貝的用法。

## 淺拷貝
### Object.assign()
```js
const person = {
    name: 'Blueberry',
    interest: ['閱讀', '玩遊戲']
};

const copy = Object.assign({}, person);

copy.name = 'Strawberry';
copy.interest[0] = '泡咖啡';
console.log('copy: ', copy);
console.log('person: ', person);

/**
 * [Output]
 *
 * copy: Object {
 *     name: 'Strawberry',
 *     interest: ['泡咖啡', '玩遊戲']
 * }
 * person: Object {
 *     name: 'Blueberry',
 *     interest: ['泡咖啡', '玩遊戲']
 * }
 */
```
這裡可以看到 `copy` 的第二層（`interest: ['閱讀', '玩遊戲']`）被修改了，結果連原本的 `person` 也一起變成 `泡咖啡`，因為 `copy` 的第二層仍是指向與原物件相同的記憶體位置。
但是 `name` 這個值就不同了，`copy` 的第一層是有被淺層拷貝成功的。

### for 迴圈
for 迴圈是最土法煉鋼的方法，它也能達到淺拷貝的效果。
```js
const a = [1, 2, 3];
const b = [];
for (let i = 0; i < a.length; i++) {
  b.push(a[i]);
}
b.push(4);
console.log(a);
console.log(b);

/**
 * [Output]
 *
 * [1, 2, 3]
 * [1, 2, 3, 4]
 * }
 */
```

### 展開運算子
[展開運算子](https://b-l-u-e-b-e-r-r-y.github.io/post/SpreadAndRestOperator/)是 ES6 新出的方法，應該是最方便的寫法了。
```js
const a = [1, 2, 3];
const b = [ ...a ];
b.push(4);
console.log(a);
console.log(b);

/**
 * [Output]
 *
 * [1, 2, 3]
 * [1, 2, 3, 4]
 * }
 */
```

## 深拷貝
在不使用其他套件的情況下，目前 Javascript 的深拷貝只有 `JSON.parse()` + `JSON.stringify()` 的寫法。

### JSON.parse(JSON.stringify())
```js
const person = {
    name: 'Blueberry',
    interest: ['閱讀', '玩遊戲']
};

const copy = JSON.parse(JSON.stringify(person)));

copy.name = 'Strawberry';
copy.interest[0] = '泡咖啡';
console.log('copy: ', copy);
console.log('person: ', person);

/**
 * [Output]
 *
 * copy: Object {
 *     name: 'Strawberry',
 *     interest: ['泡咖啡', '玩遊戲']
 * }
 * person: Object {
 *     name: 'Blueberry',
 *     interest: ['閱讀', '玩遊戲']
 * }
 */
```