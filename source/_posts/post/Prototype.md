---
title: 原型鍊 (Prototype Chain)
date: 2020-04-25
comments: true
tags: 
- Prototype
- Javascript
- w3HexSchool
categories: 
- Javascript
---

# 原型鍊 (Prototype Chain)

## 原型繼承

不同於 Java 或 C++ 這些基於類別的語言，Javascript 是基於原型（prototype-based）的，ES6 雖然有 `class` 可以使用，但那只是語法糖而已。

廣義來說，繼承就是一個物件取用另一個物件的屬性或方法。而 Javascript 的繼承是**原型繼承**，繼承的物件並不會一併將所有屬性及方法複製過來，而是透過**原型鍊**來連結其物件繼承的屬性及方法。

假設有兩個物件：animal 和 elephant，讓 elephant 繼承 animal，那麼 elephant 就可以使用 animal 的屬性及方法，而 animal 就是 elephant 的原型物件。

當我們將物件打印出來並且展開，就可以看到 `__proto__`，它就是原型物件。
![](/images/prototype/04.jpg)

## 原型鍊

在原型繼承中有提到**原型鍊**，那它是什麼呢？

<img src="/images/prototype/01.jpg" width="60%" height="60%">

有一個物件名字叫 obj，它的原型是 proto，而 proto 有一個屬性是 Prop2，那我們如果想取用 Prop2，該怎麼寫？

`obj.proto.Prop2` 嗎？不，不用這麼麻煩。

直接寫 `obj.Prop2` 就好。

**當我們想要取用 Prop2 時，Javascript 引擎就會下去找 Prop2 這個屬性，先找 obj 物件內部有沒有這個屬性，如果找不到這個屬性時，它就會往原型物件找（proto），這就是原型鍊。**

**看起來 Prop2 和 Prop3 是在主物件 obj 裡，但它們其實是在原型物件中，只是因為原型鍊（Prototype Chain）的關係才能被找到。**

> 記住，別把它和範圍鍊（Scope Chain）搞混了。範圍鍊是用來尋找取用的變數。原型鍊則跟取用物件的屬性或方法有關。

--------------------------

那如果多了一個 obj2 繼承 proto 呢？
<img src="/images/prototype/02.jpg" width="60%" height="60%">

obj 和 obj2 兩個主物件本身並不衝突，但它們繼承的 proto 卻是同一個，在記憶體中的位置也是相同的，因此如果想要取 Prop3 這個屬性，也一樣可以取得到。

## 實作原型鍊（不建議）

這裡會寫不建議，是因為 `obj.__proto__ = prototype` 這個寫法會造成效能問題，這邊會使用到只是為了 Demo，實際 Coding 的時候千萬不要這樣寫。

```js
const person = {
    firstname: 'Default',
    lastname: 'Default',
    getFullName: function() {
        return this.firstname + ' ' + this.lastname;
    }
}

const blueberry = {
    firstname: 'Blueberry',
    lastname: 'Lin'
}

// 不要這樣寫！只是為了 DEMO
blueberry.__proto__ = person;
console.log(blueberry.getFullName());  // Blueberry Lin
console.log(blueberry.firstname);      // Blueberry
```

假設我有兩個物件，blueberry 繼承了 person，所以當我想呼叫 `blueberry.getFullName()` 時，也理所當然可以使用這個方法，因為繼承就是讓一個物件可以使用原型物件的所有屬性和方法。

但是為什麼 `blueberry.firstname` 的結果會是 'Blueberry' 而不是原型物件的 'Default'？這是因為 Javascript 引擎先在 blueberry 這個主物件中找到了 firstname 屬性，所以就不會繼續往原型鍊尋找。

## 參考資料

* [《JavaScript 全攻略：克服 JS 的奇怪部分》](https://www.udemy.com/course/javascriptjs/)
