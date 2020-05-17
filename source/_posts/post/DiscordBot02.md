---
title: 【用 JS 寫一個 Discord Bot！】02 建立機器人
comments: true
banner_img: /images/dc_banner.jpg
index_img: /images/dc_banner.jpg
date: 2020-05-16 18:00:00
tags: 
- node.js
- Discord.js
- DC機器人
- Javascript
- w3HexSchool
categories: 
- node.js
- Discord Bot
---

# 前言

今天我們來寫一個具有以下功能的音樂機器人：
* 播放 YouTube 歌曲
* 暫停播放
* 結束歌曲
* 歌曲隊列

如果還不知道怎麼建立機器人，可以參考我之前寫的這篇文章：[【用 JS 寫一個 Discord Bot！】01 建立機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/)。

# 實作

## 安裝套件

要完成這些功能，必須安裝下面這三個套件。

* ### [ffmpeg-static](https://github.com/eugeneware/ffmpeg-static)
執行音樂的轉檔、串流功能
```
$ npm install ffmpeg-static
```

* ### [discordjs/opus](https://github.com/discordjs/opus)
Opus 編碼器
```
$ npm install @discordjs/opus
```

* ### [node-ytdl-core](https://github.com/fent/node-ytdl-core)
執行下載 YouTube 影片的功能
```
$ npm install ytdl-core
```

## 編寫 js

沿用[之前建立好的環境](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/#%E5%BB%BA%E7%AB%8B%E5%9F%BA%E7%A4%8E%E7%92%B0%E5%A2%83)，我們以 `discord.js` 為基礎編寫。

### 建立 config.json

在開始之前，我們先在專案中建立 config.json，放一些常用的配置。

`prefix` 設定前綴字，`command` 設定常用指令，兩者搭配起來就是 `!!play`、`!!pause`。

```json

```

### 編寫 discord.js

#### 播放 YouTube 歌曲

```js

```

這時候就可以運行看看。

```
$ node discord.js
```

![](/images/dc-bot/02/01.jpg)

#### 暫停播放



#### 結束歌曲




#### 歌曲隊列