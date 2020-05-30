---
title: 使用 Node.js + Puppeteer 來爬蟲吧！[通過 PTT 八卦板年齡限制]
comments: true
banner_img: /images/node_banner.png
index_img: /images/node_banner.png
date: 2020-05-30 15:00:00
tags: 
- node.js
- Javascript
- w3HexSchool
categories: 
- node.js
---

# 前言

如果使用前一篇[使用 Node.js 來爬蟲吧！[PTT 股票板]](https://b-l-u-e-b-e-r-r-y.github.io/post/PTTCrawler/)程式碼，試著將網址改成八卦板運行，會發現爬蟲抓不到資料的問題。

這是因為第一次訪問八卦板時，PTT 會先跳出年齡驗證，使用者如果按下已滿 18 歲的按鈕，PTT 就會將紀錄存在 Cookies。

![](/images/ptt-crawler/ptt3.jpg)

當然也可以在 requset 時帶 cookie，不過本篇文章會講解使用自動化測試工具 [Puppeteer](https://github.com/puppeteer/puppeteer) 來實作這個功能。

# 實作

## 安裝 Puppeteer

```
npm i puppeteer
```

## 引入 Puppeteer

```js
const puppeteer = require('puppeteer');
```

## 程式碼

下面程式碼主要功能是先 request 八卦板，待跳出年齡驗證的頁面後，使用 Puppeteer 去模擬使用者點擊已滿18歲按鈕，並取得跳轉後的新頁面 content。

```js
const pttUrl = 'https://www.ptt.cc/bbs/Gossiping/index.html';
request({
    url: pttUrl,
    method: "GET"
}, async (error, res, body) => {
    if (error || !body) {
        return;
    }

    let $ = cheerio.load(body); // 載入 body
    const browser = await puppeteer.launch({ headless: false });  // 啟動瀏覽器，headless 設定為 false 可以看到瀏覽器運作的情況，true 為無頭瀏覽器
    const page = await browser.newPage();
    await page.goto(pttUrl);
    const buttonSelector = 'body > div.bbs-screen.bbs-content.center.clear > form > div:nth-child(2) > button';  // 已滿18歲按鈕 selector
    await page.click(buttonSelector);  // 按下按鈕
    const content = await page.content();  // 取得新頁面的內容
    await browser.close();  // 關閉瀏覽器
});
```

## 完整程式碼

```js
const request = require("request");
const cheerio = require("cheerio");
const puppeteer = require('puppeteer');

const pttCrawler = () => {
    const pttUrl = 'https://www.ptt.cc/bbs/Gossiping/index.html';
    request({
        url: pttUrl,
        method: "GET"
    }, async (error, res, body) => {
        if (error || !body) {
            return;
        }

        let $ = cheerio.load(body); // 載入 body
        const browser = await puppeteer.launch({ headless: false });  // 啟動瀏覽器，headless 設定為 false 可以看到瀏覽器運作的情況，true 為無頭瀏覽器
        const page = await browser.newPage();
        await page.goto(pttUrl);
        const buttonSelector = 'body > div.bbs-screen.bbs-content.center.clear > form > div:nth-child(2) > button';  // 已滿18歲按鈕 selector
        await page.click(buttonSelector);  // 按下按鈕

        const content = await page.content();  // 取得新頁面的內容
        $ = cheerio.load(content);

        const result = []; // 建立一個儲存結果的容器
        const list = $(".r-list-container .r-ent");

        for (let i = 0; i < list.length; i++) {
            const title = list.eq(i).find('.title a').text();
            const author = list.eq(i).find('.meta .author').text();
            const date = list.eq(i).find('.meta .date').text();
            const link = list.eq(i).find('.title a').attr('href');

            result.push({ title, author, date, link });
        }

        console.log(result);
        await browser.close();  // 關閉瀏覽器
    });
};

pttCrawler();
```