---
title: Destructuring 解構賦值
comments: true
date: 2020-02-22 19:43:00
tags: Javascript
categories: 
- ES6
- Javascript
---
解構賦值可以用在陣列或物件，可以提取特定的值成獨立變數。

# 1. Array Destructuring
```js
const numbers = [1, 2, 3];
[num1, num2] = numbers;

console.log(num1, num2);

/* Output:
   1
   2 */
```

如果只想要提取 1 和 3 的值，只要在左邊陣列的中間部分空一格即可：
```js
const numbers = [1, 2, 3];
const [num1, , num3] = numbers;

console.log(num1, num3);

/* Output:
   1
   3 */
```

# 2. Object Destructuring
```js
let { name: n, age: a } = {
    name: 'Blueberry',
    age: 24
}

console.log(n);  // Blueberry
console.log(a);  // 24
```