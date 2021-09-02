---
title: Event Loop (事件循環)
date: 2021-08-25
comments: true
categories: 
- Javascript
---

# Event Loop (事件循環)

:::note

此文為 [所以說event loop到底是什麼玩意兒？| Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf) 個人重點整理筆記
如有侵權請來信 wl00535428@gmail.com 我會用最快的速度刪除，謝謝
:::

{%youtube 8aGhZQkoFbQ %}

## 專有名詞

- **JavsScript**：擁有 Call Stack (呼叫堆疊)、Event Loop (事件循環)、Callback Queue (回調佇列) 及一些 API
- **V8 引擎**：擁有 Call Stack (執行堆疊) 及 heap (堆積)

然而 setTimeout、DOM、HTTP Request 之類的東西並不在 V8 引擎裡，而是在瀏覽器提供的 **Web API** 裡面。

在說 Event Loop 之前，先來說說 Call Stack。

------------

## Call Stack (呼叫堆疊)

JavaScript 是單執行緒的程式語言，有著單執行緒的 Call Stack，意思就是它只能一次做一件事。

Call Stack 基本上是一種資料結構，它能夠記錄幾種情況下我們在程式中的哪個位置。在呼叫任何子程式時，主程式都必須暫存子程式執行完畢後應該返回到的位置。

來看看講者提供的範例：

```javascript
function multiply(a, b) {
    return a * b;
}

function square(n) {
    return multiply(n, n);
}

function printSquare(n) {
    var squared = square(n);
    console.log(squared);
}

printSquare(4);
```

第一步是先將呼叫 `printSquare(4)` 的這段 anonymous function（下圖稱為 `main()`）放到 Stack。

然後就是把程式逐步執行的函式放到 Stack。

![](https://i.imgur.com/80pfH2K.png)

函式執行完後再從 Stack 移除，最終會清空 Stack。這就是 Call Stack 的運作模式。

### 堆疊爆炸

當 Stack 不斷增長，直到瀏覽器內存耗盡時，瀏覽器跳出警告，稱為堆疊爆炸。

![](https://i.imgur.com/GhhucS9.png)

### 阻塞 (Blocking)

假設今天有一段 Code 如下，分別跑三個 HTTP Request（註：為了幫助理解，這邊先不管非同步回調）

```javascript
var foo = $.getSync('//foo.com');
var bar = $.getSync('//bar.com');
var qux = $.getSync('//qux.com');

console.log(foo);
console.log(bar);
console.log(qux);
```

![](https://i.imgur.com/ccKoLNn.png)

因為單執行緒的關係，瀏覽器會因為正在跑 HTTP Request 而變得什麼都不能做，這就稱為阻塞。

避免阻塞最簡單的解法就是非同步回調，所以瀏覽器中幾乎沒有阻塞式函式，Node 也是，幾乎都是非同步函式。

### 請記住這張圖

![](https://i.imgur.com/EdWf21c.png)

我們能夠同時做事的原因是因為**瀏覽器不只有 Runtime 而已**。

JavaScript Runtime 一次只能做一件事，但瀏覽器會提供給我們其他東西，例如一些 Web APIs。

Node.js 也是一樣的原理，但使用的不是 WebAPI，而是 C++ API，C++ API 會讓執行緒從我們眼前隱藏起來。

------------

## Event Loop (事件循環)

所以 Event Loop 到底是什麼？

它就像是整個方程式中最單純的小拼圖，只有一個非常簡單的工作：**查看 Stack、然後查看 Callback Queue (下圖稱為 task queue)**。

如果 Stack 是空的，它會將第一個東西放到 Callback Queue 上，並將其堆到 Stack 上，讓 Stack 能有效執行。

![](https://i.imgur.com/SgLSUpw.gif)

以這段程式碼為例：

```javascript=
console.log('Hi');

setTimeout(function cb() {
    console.log('there');
}, 0);

console.log('JSConfEU');
```

下面就來完整的跑一次整個流程（截至原影片的 15:36 - 16:13 處）：

<iframe width="560" height="315" src="https://www.youtube.com/embed/8aGhZQkoFbQ?start=936&amp;end=973" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### 理解瀏覽器的畫面刷新與 Event Loop 的關係

講者寫了一個網站 [Loupe](http://latentflip.com/loupe/)，可以把程式碼貼上，看看背後發生了什麼事。

左上角有一個工具圖案，展開後可以調整每一個步驟的 Delay 時間及顯示 Simulate Renders，Simulate Renders 會模擬頁面渲染的時機。

這邊特別說一下其中一個範例，講者提供了 Synchronous 和 Asynchronous 版本的 forEach，來試著模擬處理資料時會發生的事情。

```javascript
// Synchronous
[1, 2, 3, 4].forEach(function (i) {
    console.log(i);
});

// Asynchronous
function asyncForEach(array, cb) {
    array.forEach(function b() {
        setTimeout(cb, 0);
    });
}

asyncForEach([1, 2, 3, 4], function a (i) {
    console.log(i);
});
```

如果把 Synchronous 的 forEach 貼到 [Loupe](http://latentflip.com/loupe/)，並打勾顯示 Simulate Renders，會發現 Render Queue 那邊呈現紅底，也就是頁面會卡住不動，無法點擊按鈕、選取文字，整個阻塞住直到 forEach 跑完為止。

這是因為瀏覽器被我們在 JavaScript 中所做的操作所限制，所以當 Call Stack 上有程式碼的時候，瀏覽器畫面就沒有辦法更新。

也就是 Render Queue 必須等到 Call Stack 清空時才會更新畫面。

如果是 Asynchronous 就不同了，**它會先把 setTimeout 事件加入 Callback Queue，Event Loop 再把事件從 Callback Queue 一個個推到 Call Stack 消化。**

**在 Call Stack 消化完當前任務時，等待 Event Loop 把事件推到 Call Stack 前會有一些空檔時間，讓 Render Queue 刷新畫面，因為 Render Queue 的優先級別高於 Callback Queue，而瀏覽器每 16 毫秒就會把 Render 排進佇列，所以看起來畫面就不會卡住。** (建議實際到 [Loupe](http://latentflip.com/loupe/) 感受)

如果沒有注意程式碼是怎麼被排進佇列的話，就很容易讓畫面變得遲鈍。

所以如果有人說：*"Don't blocking the event loop"*，就是在說**不要把耗時的程式碼直接放到 Call Stack 處理**，應該善用 Asynchronous Function 來打造流暢的畫面體驗。

------------

## 參考資料

- [所以說event loop到底是什麼玩意兒？| Philip Roberts | JSConf EU](https://www.youtube.com/watch?v=8aGhZQkoFbQ&ab_channel=JSConf)
- [呼叫堆疊 - 維基百科](https://zh.wikipedia.org/wiki/%E5%91%BC%E5%8F%AB%E5%A0%86%E7%96%8A)
- [Loupe](http://latentflip.com/loupe/)