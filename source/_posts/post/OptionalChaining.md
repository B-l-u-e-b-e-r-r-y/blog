---
title: Optional Chaining (可選串連)
date: 2021-08-30
comments: true
categories: 
- Javascript
---

# Optional Chaining (可選串連)

## 使用情境

假設今天有一些資料長這樣：

```javascript
const users = [
    {
        name: 'Blueberry',
        github: {
            account: 'B-l-u-e-b-e-r-r-y',
            url: 'https://github.com/B-l-u-e-b-e-r-r-y'
        }
    }, {
        name: 'Foo',
        github: null
    }
]
```

然後我想要用 map 取得 User 的 Github 帳號——

```javascript
users.map(user => user.github.account);
```

因為 Foo 的 github 為 `null`，所以會報錯：

> Cannot read property 'account' of null

當然，也可以改成這樣：

```javascript
users.map(user => user.github && user.github.account);
```

但如果今天的資料格式變成了……

```javascript
const users = [
    {
        name: 'Blueberry',
        github: {
            account: 'B-l-u-e-b-e-r-r-y',
            url: 'https://github.com/B-l-u-e-b-e-r-r-y',
            repositories: [
                {
                    name: 'Discord-Bot-01',
                    url: 'https://github.com/B-l-u-e-b-e-r-r-y/Discord-Bot-01',
                    files: ['discord.js', 'README.md'],
                    releases: [
                        {
                            version: '1.0',
                            releaseDate: '2021-08-20',
                            description: '...'
                        }
                    ]
                }
            ]
        }
    }, {
        name: 'Foo',
        github: null
    }
]

// 取 releases 的 releaseDate
users.map(user => 
    user.github
    && user.github.repositories
    && user.github.repositories.length > 0
    && user.github.repositories[0].releases
    && user.github.repositories[0].releases.length > 0
    && user.github.repositories[0].releases[0].releaseDate);
```

這樣就會很辛苦 QQ

## 使用方法

所幸現在 Chrome 支援 Optional Chaining，能夠讓上面落落長的程式碼只用一行搞定。

```javascript
users.map(user => user.github?.repositories?.[0]?.releases?.[0]?.releaseDate);
```

運作流程如下：

* user 如果存在 github 就繼續往下查找
    * user 如果不存在 github 就回傳 `undefined`
* github 如果存在 repositories 就繼續往下查找
* repositories 如果存在 repositories[0] 就繼續往下查找

...以此類推。

目前 Optional chaining 除了 IE 以外的瀏覽器都支援，也支援 Node.js v14.0.0 版本以上，詳細可以看[瀏覽器相容性](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/Optional_chaining#%E7%80%8F%E8%A6%BD%E5%99%A8%E7%9B%B8%E5%AE%B9%E6%80%A7)。

---------

## 參考資料

- [可選串連 - MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Operators/Optional_chaining)