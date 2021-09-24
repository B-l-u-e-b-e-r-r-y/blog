---
title: 【用 JS 寫一個 Discord Bot！】02 音樂機器人
date: 2020-05-17
tags: 
- Discord Bot
categories: 
- node.js
---

# 【用 JS 寫一個 Discord Bot！】02 音樂機器人

今天我們來寫一個具有以下功能的音樂機器人：
* 播放 YouTube 歌曲
* 暫停/恢復播放
* 跳過歌曲
* 歌曲隊列

如果還不知道怎麼建立機器人，可以參考我之前寫的這篇文章：[【用 JS 寫一個 Discord Bot！】01 建立機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/)。

## 實作

必備：

* Node.js 版本 v14.0.0 以上
* 編輯器 (推薦 Visual Studio Code)

### 安裝套件

* #### [Discord.js](https://github.com/discordjs/discord.js/) v12.5.3
Discord 機器人的核心套件
```bash
$ npm install discord.js@12.5.3
```

* #### [ffmpeg-static](https://github.com/eugeneware/ffmpeg-static)
執行音樂的轉檔、串流功能
```bash
$ npm install ffmpeg-static
```

* #### [discordjs/opus](https://github.com/discordjs/opus)
Opus 編碼器
```bash
$ npm install @discordjs/opus
```

* #### [node-ytdl-core](https://github.com/fent/node-ytdl-core)
執行下載 YouTube 影片的功能
```bash
$ npm install ytdl-core
```

### 開始編寫

#### 建立 config.json

在開始之前，我們先在專案中建立 config.json，放一些常用配置。

`prefix` 設定指令的前綴字。

```json
{
    "prefix": "!!"
}
```

#### 編寫 discord.js

```js
const { Client } = require('discord.js');
const ytdl = require('ytdl-core');
const { token } = require('./token.json');
const { prefix } = require('./config.json');
const client = new Client();

// 建立一個類別來管理 Property 及 Method
class Music {

    constructor() {
        /**
         * 下面的物件都是以 Discord guild id 當 key，例如：
         * this.isPlaying = {
         *     724145832802385970: false
         * }
         */

        /**
         * 機器人是否正在播放音樂
         * this.isPlaying = {
         *     724145832802385970: false
         * }
         */
        this.isPlaying = {};

        /**
         * 等待播放的音樂隊列，例如：
         * this.queue = {
         *     724145832802385970: [{
         *         name: 'G.E.M.鄧紫棋【好想好想你 Missing You】Official Music Video',
         *         url: 'https://www.youtube.com/watch?v=P6QXo88IG2c&ab_channel=GEM%E9%84%A7%E7%B4%AB%E6%A3%8B'
         *     }]
         * }
         */
        this.queue = {};

        // https://discord.js.org/#/docs/main/stable/class/VoiceConnection
        this.connection = {};

        // https://discord.js.org/#/docs/main/stable/class/StreamDispatcher
        this.dispatcher = {};
    }

    async join(msg) {

        // 如果使用者正在頻道中
        if (msg.member.voice.channel !== null) {
            // Bot 加入語音頻道
            this.connection[msg.guild.id] = await msg.member.voice.channel.join();
        } else {
            msg.channel.send('請先進入語音頻道');
        }

    }

    async play(msg) {

        // 語音群的 ID
        const guildID = msg.guild.id;

        // 如果 Bot 還沒加入該語音群的語音頻道
        if (!this.connection[guildID]) {
            msg.channel.send('請先將機器人 `!!join` 加入頻道');
            return;
        }

        // 如果 Bot leave 後又未加入語音頻道
        if (this.connection[guildID].status === 4) {
            msg.channel.send('請先將機器人 `!!join` 重新加入頻道');
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
            if (this.isPlaying[guildID]) {
                msg.channel.send(`歌曲加入隊列：${info.title}`);
            } else {
                this.isPlaying[guildID] = true;
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
        this.dispatcher[guildID].on('finish', () => {

            // 如果隊列中有歌曲
            if (this.queue[guildID].length > 0) {
                this.playMusic(msg, guildID, this.queue[guildID][0]);
            } else {
                this.isPlaying[guildID] = false;
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

        // 如果機器人在頻道中
        if (this.connection[msg.guild.id] && this.connection[msg.guild.id].status === 0) {

            // 如果機器人有播放過歌曲
            if (this.queue.hasOwnProperty(msg.guild.id)) {

                // 清空播放列表
                delete this.queue[msg.guild.id];

                // 改變 isPlaying 狀態為 false
                this.isPlaying[msg.guild.id] = false;
            }

            // 離開頻道
            this.connection[msg.guild.id].disconnect();
        } else {
            msg.channel.send('機器人未加入任何頻道');
        }

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
* 【讓機器人離開語音頻道（會清空歌曲隊列）】`!!leave`

可以自行玩玩看。

```bash
$ node discord.js
```

![](/images/dc-bot/02/02.jpg)

本來想把程式碼切開在文中講解，但是發現這樣寫起來會篇幅太長而且雜亂，所以就乾脆把註解寫在 code 裡面。

**本次音樂機器人的 [Github Repository](https://github.com/B-l-u-e-b-e-r-r-y/Discord-Bot-02)**，可以自行 clone 下來研究或修改。

## FAQ

這邊蒐集了可能會遇到的問題。

<details>
<summary>點我展開</summary>

* Q：執行程式後出現 `Error: Cannot find module 'xxx'` 怎麼辦？
* A：通常那個 `xxx` 是缺少安裝的套件。跑一下安裝套件的指令 `npm i 套件名稱` 應該就正常了。
------
* Q：下 play 指令後機器人跳出 `MinigetError: Status code: 404` 的錯誤訊息怎麼辦？
* A：請更新 [node-ytdl-core](https://github.com/fent/node-ytdl-core) 至最新版本。`npm i node-ytdl-core@latest`
------
* Q：播放音樂時，先 `!!pause` 後再 `!!resume`，機器人沒有恢復播放怎麼辦？
* A：與 discord.js 和 node.js 版本有關。目前只能等 discord.js 修復或自降 node.js 版本。
詳細請參考這個 issue：[The function dispatcher.pause() and dispatcher.resume() only works on a second switch #5300](https://github.com/discordjs/discord.js/issues/5300)
</details>

### 目前可正常運行的版本（參考用）

* node.js: v14.16
* @discordjs/opus: "0.5.0"
* discord.js: "12.5.3"
* ffmpeg-static: "4.2.8"
* ytdl-core: "4.8.3"

## 文章更新記錄

<details>
<summary>點我展開</summary>

### 2021/01/13 更新

發現之前會有無法播放的問題，已更新 ytdl 套件解決問題。

### 2021/02/03 更新

發現更新 ytdl 套件後會無法播放音樂的問題，因為 `musicURL` 字串的空格沒處理乾淨：
```js
const musicURL = msg.content.replace(`${prefix}play`, '');
```
改為：
```js
const musicURL = msg.content.replace(`${prefix}play`, '').trim();
```

### 2021/05/30 更新

發現機器人無法於不同伺服器播放歌曲的問題，是因為原本的 `isPlaying` 變數忘了寫成物件，變成多個伺服器共用一個變數。
```js
this.isPlaying = false;
```
改為：
```js
this.isPlaying = {};  // { guild1ID: false, guild2ID: true, ... }
```

### 2021/06/27 更新

加入一些防呆機制以及更新套件版本。

舊朋友如果有遇到問題，請試著更新 [node-ytdl-core](https://github.com/fent/node-ytdl-core) 為最新版本（4.8.3）：

```bash
npm install ytdl-core@latest
```

### 2021/09/22 更新

改為統一使用 v12.5.3 的 discord.js。
</details>

## 碎碎念

在寫 Discord 機器人系列文章前，完全沒想過會有這麼多人看。

能夠幫助到這麼多人我覺得很開心，有部分讀者遇到問題會在下面發問，我也樂意解答，多數得不到回應，我當成是自己的回答有幫助讀者解決問題。

但是隨著讀者越來越多、問題越來越多，我決定**不再回答重複的問題**，請各位在發問前先看看 FAQ 及下面的留言區，查找有沒有人問過自己遇到的問題。

另外就是......發問時請具備基本禮貌，請尊重願意幫你解答問題的人，感謝配合。

------------------------------------------

**【用 JS 寫一個 Discord Bot！】系列文章**

* [【用 JS 寫一個 Discord Bot！】01 建立機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/)
* [【用 JS 寫一個 Discord Bot！】02 音樂機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot02/)