---
title: 展開運算子 (Spread Operator) 與其餘運算子 (Rest Operator)
date: 2020-01-11
comments: true
tags: 
- ES6
categories: 
- Javascript
---

# 展開運算子 (Spread Operator) 與其餘運算子 (Rest Operator)

這兩種運算子的寫法都是 `...`，對，你沒看錯，就是三個點！

接下來來實際說明使用情境：

## 展開運算子 (Spread Operator)

用來將舊的陣列或物件複製到新的陣列或物件中，算是淺拷貝 (Shallow Copy) 的一種。

### 陣列範例

```js
const numbers = [1, 2, 3];
const newNumbers = [...number, 4];

console.log(newNumbers);

/* Output:
   [1, 2, 3, 4] */
```

### 物件範例

```js
const person = {
    name: 'Blueberry'
};

const newPerson = {
    ...person,
    age: 24
};

console.log(newPerson);

/* Output:
   {
       age: 24,
       name: 'Blueberry'
   } */
```

## 其餘運算子 (Rest Operator)

將不確定數量的參數視為一個陣列。

範例示範將多個數字傳入函式，並用 filter 找出 args 陣列中等於 1 的數字：

```js
const equalOne = (...args) => {
    return args.filter(el => el === 1);
}

console.log(equalOne(1, 2, 3, 4));

/* Output:
   [1] */
```