---
title: Destructuring 解構賦值
comments: true
date: 2020-02-22 19:43:00
tags: 
- Javascript
- ES6
- w3HexSchool
categories: 
- Javascript
---
解構賦值可以用在陣列或物件，可以提取特定的值成獨立變數。

# 1. Array Destructuring
```js
const numbers = [1, 2, 3];
[num1, num2] = numbers;

console.log(num1, num2);

/*
    Output:
    1
    2
*/
```

如果只想要提取 1 和 3 的值，只要在左邊陣列的中間部分空一格即可：
```js
const numbers = [1, 2, 3];
const [num1, , num3] = numbers;

console.log(num1, num3);

/*
    Output:
    1
    3
*/
```

也可以使用其餘運算子接下剩下的變數：
```js
const numbers = [1, 2, 3];
const [num, ...nums] = numbers;

console.log(num, nums);

/* 
    Output:
    1
    [2, 3]
*/ 
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

物件解構賦值也同樣可以使用展開運算子：
```js
let { name, ...others } = {
    name: 'Blueberry',
    age: 24,
    color: blue
}

console.log(name, others);

/* 
    Output: Blueberry
    Object {
        age: 24,
        color: "blue"
    }
*/
```