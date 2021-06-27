---
title: 使用 Node.js 來爬蟲吧！[PTT 股票板]
comments: true
banner_img: /images/node_banner.png
index_img: /images/node_banner.png
date: 2020-03-28 18:00:00
tags: 
- node.js
- Javascript
- w3HexSchool
categories: 
- node.js
---
# 前言

一般大家爬蟲都是使用 python，其實 node.js 也是可以爬蟲的，作為前端，能用自己習慣的語言來寫當然很方便。

但是 python 和 node.js 除了寫法不同，兩者特性也是不同的，例如 node.js 是異步的、單執行緒，python 則是多執行緒等等，關於這些差異本文就不多加討論。

# 模組

node.js 爬蟲需要用到兩個模組：[request](https://github.com/request/request) 和 [cheerio](https://github.com/cheeriojs/cheerio)。

## 介紹

**request** 是用來訪問網站用的，它能模擬 Client 訪問網站，設定訪問時帶的 Header。

**cheerio** 算是 node.js 界的 jQuery，它是以 jQuery 為核心去設計的，好比要抓取一個 html，class 名稱為 title 的文字 `<div class='title'>標題</div>`，cheerio 的寫法是這樣：`$('.title').text()`。

## 安裝

```
$ npm i --save request
$ npm i --save cheerio
```

## 載入

安裝好後就可以看到專案的 package.json 已經出現這兩個模組，這時候就可以把模組引入程式中。

```js
const request = require("request");
const cheerio = require("cheerio");
```

# 實作

## request 訪問

本文以 [PTT 股票板](https://www.ptt.cc/bbs/Stock/index.html) 為範例。

先發送 request，訪問股票板。

```js
const pttCrawler = () => {
    request({
        url: "https://www.ptt.cc/bbs/Stock/index.html",
        method: "GET"
    }, (error, res, body) => {
        // 如果有錯誤訊息，或沒有 body(內容)，就 return
        if (error || !body) {
            return;
        }
    });
};

pttCrawler();
```

## cheerio 抓取 Element

request 之後就可以取得內容(body)，接著使用 cheerio 抓取 Element。

寫程式碼之前，可以先到股票板，按右鍵 > 檢查，看看 Element 的名稱是什麼。

如下圖，紅框就是要抓的部分：

![](/images/ptt-crawler/ptt1.jpg)

因為它是父元素(`.r-list-container`)裡包著許多子元素(`.r-ent`)的結構，所以這邊要使用到迴圈。

```js
const pttCrawler = () => {
    request({
        url: "https://www.ptt.cc/bbs/Stock/index.html",
        method: "GET"
    }, (error, res, body) => {
        // 如果有錯誤訊息，或沒有 body(內容)，就 return
        if (error || !body) {
            return;
        }

        const data = [];
        const $ = cheerio.load(body); // 載入 body
        const list = $(".r-list-container .r-ent");
        for (let i = 0; i < list.length; i++) {
            const title = list.eq(i).find('.title a').text();
            const author = list.eq(i).find('.meta .author').text();
            const date = list.eq(i).find('.meta .date').text();
            const link = list.eq(i).find('.title a').attr('href');

            data.push({ title, author, date, link });
        }

        console.log(data);
    });
};

pttCrawler();
```

執行程式後就可以看到結果囉！

![](/images/ptt-crawler/ptt2.jpg)

## 完整程式碼

```js
const request = require("request");
const cheerio = require("cheerio");

const pttCrawler = () => {
    request({
        url: "https://www.ptt.cc/bbs/Stock/index.html",
        method: "GET"
    }, (error, res, body) => {
        // 如果有錯誤訊息，或沒有 body(內容)，就 return
        if (error || !body) {
            return;
        }

        const data = [];
        const $ = cheerio.load(body); // 載入 body
        const list = $(".r-list-container .r-ent");
        for (let i = 0; i < list.length; i++) {
            const title = list.eq(i).find('.title a').text();
            const author = list.eq(i).find('.meta .author').text();
            const date = list.eq(i).find('.meta .date').text();
            const link = list.eq(i).find('.title a').attr('href');

            data.push({ title, author, date, link });
        }

        console.log(data);
    });
};

pttCrawler();
```