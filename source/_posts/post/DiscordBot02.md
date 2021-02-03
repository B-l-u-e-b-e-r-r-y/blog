---
title: 【用 JS 寫一個 Discord Bot！】02 音樂機器人
comments: true
banner_img: /images/dc_banner.jpg
index_img: /images/dc_banner.jpg
date: 2020-05-17 23:00:00
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
* 暫停/恢復播放
* 跳過歌曲
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

## 開始編寫

### 建立 config.json

在開始之前，我們先在專案中建立 config.json，放一些常用配置。

`prefix` 設定指令的前綴字。

```json
{
    "prefix": "!!"
}
```

### 編寫 discord.js

```js
const { Client } = require('discord.js');
const ytdl = require('ytdl-core');
const { token } = require('./token.json');
const { prefix } = require('./config.json');
const client = new Client();

// 建立一個類別來管理 Property 及 Method
class Music {

    constructor() {
        this.isPlaying = false;
        this.queue = {};
        this.connection = {};
        this.dispatcher = {};
    }

    async join(msg) {

        // Bot 加入語音頻道
        this.connection[msg.guild.id] = await msg.member.voice.channel.join();

    }

    async play(msg) {

        // 語音群的 ID
        const guildID = msg.guild.id;

        // 如果 Bot 還沒加入該語音群的語音頻道
        if (!this.connection[guildID]) {
            msg.channel.send('請先加入頻道');
            return;
        }

        // 處理字串，將 !!play 字串拿掉，只留下 YouTube 網址
        const musicURL = msg.content.replace(`${prefix}play`, '').trim();

        try {

            // 取得 YouTube 影片資訊
            const res = await ytdl.getInfo(musicURL);
            const info = res.videoDetails;

            // 將歌曲資訊加入隊列
            if (!this.queue[guildID]) {
                this.queue[guildID] = [];
            }

            this.queue[guildID].push({
                name: info.title,
                url: musicURL
            });

            // 如果目前正在播放歌曲就加入隊列，反之則播放歌曲
            if (this.isPlaying) {
                msg.channel.send(`歌曲加入隊列：${info.title}`);
            } else {
                this.isPlaying = true;
                this.playMusic(msg, guildID, this.queue[guildID][0]);
            }

        } catch(e) {
            console.log(e);
        }

    }

    playMusic(msg, guildID, musicInfo) {

        // 提示播放音樂
        msg.channel.send(`播放音樂：${musicInfo.name}`);

        // 播放音樂
        this.dispatcher[guildID] = this.connection[guildID].play(ytdl(musicInfo.url, { filter: 'audioonly' }));

        // 把音量降 50%，不然第一次容易被機器人的音量嚇到 QQ
        this.dispatcher[guildID].setVolume(0.5);

        // 移除 queue 中目前播放的歌曲
        this.queue[guildID].shift();

        // 歌曲播放結束時的事件
        const self = this;
        this.dispatcher[guildID].on('finish', () => {

            // 如果隊列中有歌曲
            if (self.queue[guildID].length > 0) {
                self.playMusic(msg, guildID, self.queue[guildID].shift());
            } else {
                self.isPlaying = false;
                msg.channel.send('目前沒有音樂了，請加入音樂 :D');
            }

        });

    }

    resume(msg) {

        if (this.dispatcher[msg.guild.id]) {
            msg.channel.send('恢復播放');

            // 恢復播放
            this.dispatcher[msg.guild.id].resume();
        }

    }

    pause(msg) {

        if (this.dispatcher[msg.guild.id]) {
            msg.channel.send('暫停播放');

            // 暫停播放
            this.dispatcher[msg.guild.id].pause();
        }

    }

    skip(msg) {

        if (this.dispatcher[msg.guild.id]) {
            msg.channel.send('跳過目前歌曲');

            // 跳過歌曲
            this.dispatcher[msg.guild.id].end();
        }

    }

    nowQueue(msg) {

        // 如果隊列中有歌曲就顯示
        if (this.queue[msg.guild.id] && this.queue[msg.guild.id].length > 0) {
            // 字串處理，將 Object 組成字串
            const queueString = this.queue[msg.guild.id].map((item, index) => `[${index+1}] ${item.name}`).join();
            msg.channel.send(queueString);
        } else {
            msg.channel.send('目前隊列中沒有歌曲');
        }

    }

    leave(msg) {

        // 離開頻道
        this.connection[msg.guild.id].disconnect();

    }
}

const music = new Music();

// 當 Bot 接收到訊息時的事件
client.on('message', async (msg) => {

    // 如果發送訊息的地方不是語音群（可能是私人），就 return
    if (!msg.guild) return;

    // !!join
    if (msg.content === `${prefix}join`) {

        // 機器人加入語音頻道
        music.join(msg);
    }

    // 如果使用者輸入的內容中包含 !!play
    if (msg.content.indexOf(`${prefix}play`) > -1) {

        // 如果使用者在語音頻道中
        if (msg.member.voice.channel) {

            // 播放音樂
            await music.play(msg);
        } else {

            // 如果使用者不在任何一個語音頻道
            msg.reply('你必須先加入語音頻道');
        }
    }

    // !!resume
    if (msg.content === `${prefix}resume`) {

        // 恢復音樂
        music.resume(msg);
    }

    // !!pause
    if (msg.content === `${prefix}pause`) {

        // 暫停音樂
        music.pause(msg);
    }

    // !!skip
    if (msg.content === `${prefix}skip`) {

        // 跳過音樂
        music.skip(msg);
    }

    // !!queue
    if (msg.content === `${prefix}queue`) {

        // 查看隊列
        music.nowQueue(msg);
    }

    // !!leave
    if (msg.content === `${prefix}leave`) {

        // 機器人離開頻道
        music.leave(msg);
    }
});

// 連上線時的事件
client.on('ready', () => {
    console.log(`Logged in as ${client.user.tag}!`);
});

client.login(token);
```

寫完後就可以運行看看。

操作流程是先 `!!join` 讓機器人加入語音頻道→ `!!play 音樂網址` 播放音樂或加入隊列（如果音樂正在播放）。

功能如下：
* 【機器人加入語音】`!!join`
* 【播放音樂（加入隊列）】`!!play 音樂網址`
* 【暫停播放】`!!pause`
* 【恢復播放】`!!resume`
* 【跳過這首歌曲】`!!skip`
* 【查看歌曲隊列】`!!queue`
* 【讓機器人離開語音頻道】`!!leave`

可以自行玩玩看。

```
$ node discord.js
```

![](/images/dc-bot/02/02.jpg)

本來想把程式碼切開在文中講解，但是發現這樣寫起來會篇幅太長而且雜亂，所以就乾脆把註解寫在 code 裡面。

本次音樂機器人的 [Github Repo](https://github.com/B-l-u-e-b-e-r-r-y/Discord-Bot-02)，可以自行 clone 下來研究或修改。

## 2021/01/13 更新

發現之前會有無法播放的問題，已更新 ytdl 套件解決問題

## 2021/02/03 更新

發現更新 ytdl 套件後會無法播放音樂的問題，因為 `musicURL` 字串的空格沒處理乾淨
```js
const musicURL = msg.content.replace(`${prefix}play`, '');
```
改為：
```js
const musicURL = msg.content.replace(`${prefix}play`, '').trim();
```

------------------------------------------

**【用 JS 寫一個 Discord Bot！】系列文章**

[【用 JS 寫一個 Discord Bot！】01 建立機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/)
[【用 JS 寫一個 Discord Bot！】02 音樂機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot02/)