---
title: First-Class Function (一級函式)
date: 2021-08-26
comments: true
categories: 
- Javascript
---

# First-Class Function (一級函式)

如果在一個程式語言中，一個函式能被當作參數使用，就能說**該程式語言擁有一級函式**。

由於 JavaScript 擁有 First-Class Function (一級函式) 的關係，我們可以：

## 1. 指定參數為函式

```javascript
const bar = function () {
    console.log('bar');
}

bar();
```

## 2. 指定參數為函式並傳入函式
```javascript
function enGreet (name) {
    return `Hello ${name}!`;
}

function greeting (greetFn, name) {
    console.log(greetFn(name));
}

greeting(enGreet, 'Blueberry');  // Hello Blueberry!
```

## 3. 返回函數
```javascript
function greeting (name) {
    return function () {
        console.log(`Hello ${name}!`);
    }
}

greeting('Blueberry')();  // Hello Blueberry!
```

當一個函式可以回傳另一個函式時，我們稱之為高階函式 (Higher-Order Function)。

## 參考資料

- [MDN - 一級函式（First-class Function）](https://developer.mozilla.org/zh-TW/docs/Glossary/First-class_Function)