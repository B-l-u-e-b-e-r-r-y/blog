---
title: 【用 JS 寫一個 Discord Bot！】01 建立機器人
comments: true
banner_img: /images/dc_banner.jpg
index_img: /images/dc_banner.jpg
date: 2020-04-04 18:00:00
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

## 創建機器人

首先到 [Discord Developer](https://discordapp.com/developers/applications/) 建立機器人。（需登入）

### 1. 新增 APP

進入網頁後，點擊按鈕 New Application。

![](/images/dc-bot/01/01.jpg)

### 2. 設定 APP 名稱

![](/images/dc-bot/01/02.jpg)

提醒一下，APP名稱會出現在這邊：

![](/images/dc-bot/01/02-1.jpg)

### 3. 點選左邊的 Bot

![](/images/dc-bot/01/03.jpg)

### 4. 新增 Bot

點擊 Add Bot。

![](/images/dc-bot/01/04.jpg)

Bot 新增後可以更改它的 USERNAME、ICON（頭貼），修改完畢後記得儲存。

![](/images/dc-bot/01/05.jpg)

這裡的 Token 指的是機器人的秘鑰，編寫程式時需要加入這串 token。

**切忌不要將 token 洩漏出去**，因為擁有 token 的人可以操控這隻 bot。

題外話：我曾經不小心把 token 上傳到 Github 上面過（當時還沒有開放私有庫），結果不到短短的一小時就被盜用，那時候整個 DC 群組的人都收到了廣告訊息...XD

**※ 2021/06/27 補充：現在如果不小心把 token 上傳到 Github，Discord 官方會用 Discord 通知 token 擁有人並強制重新產生 token。**

如果 token 不小心洩漏，可以點擊 **Regenerate** 重新產生 token。

![](/images/dc-bot/01/06.jpg)

### 5. 設定機器人權限

拉到最下面可以看到 **Bot Permissions**，這裡可以設定機器人的權限。

勾選完後記得將 PERMISSIONS INTEGER 記錄下來。

![](/images/dc-bot/01/09.jpg)

### 6. 邀請機器人至伺服器

回到 General Information，複製 APP 的 CLIENT ID。

![](/images/dc-bot/01/07.jpg)

邀請 url 的格式如下，將剛才複製的 CLIENT ID 與 PERMISSIONS INTEGER 替換即可：

`https://discordapp.com/oauth2/authorize?&client_id=CLIENT_ID&scope=bot&permissions=PERMISSIONS_INTEGER`

範例如下：

[https://discordapp.com/oauth2/authorize?&client_id=695898879895404584&scope=bot&permissions=8](https://discordapp.com/oauth2/authorize?&client_id=695898879895404584&scope=bot&permissions=8)
![](/images/dc-bot/01/08.jpg)

機器人進入伺服器後會是離線狀態，這完全是正常的，接下來我們就要用 Node.js 編寫機器人的功能。

## 建立基礎環境

必備：Node.js 版本 v14.0.0 以上、編輯器 (推薦 Visual Studio Code)

### 1. 安裝 [Discord.js](https://github.com/discordjs/discord.js/)

```
$ npm install discord.js
```

### 2. 新增 token.json

```json
{
    "token": "Token"
}
```

Token 在 Bot 頁面複製：

![](/images/dc-bot/01/06.jpg)

`※ 如果專案要上傳至 Github，切記將 token.json 加入 .gitignore。`

### 3. 新增 discord.js

於專案中新增 discord.js，這邊我們使用 [Discord.js](https://github.com/discordjs/discord.js/) 的官方範例。

```js
const Discord = require('discord.js');
const { token } = require('./token.json');
const client = new Discord.Client();

// 連上線時的事件
client.on('ready', () => {
    console.log(`Logged in as ${client.user.tag}!`);
});

// 當 Bot 接收到訊息時的事件
client.on('message', msg => {
    // 如果訊息的內容是 'ping'
    if (msg.content === 'ping') {
        // 則 Bot 回應 'Pong'
        msg.reply('pong');
    }
});

client.login(token);
```

### 4. 運行程式

讓機器人上線吧！

```
$ node discord.js
```

![](/images/dc-bot/01/11.jpg)

測試一下效果：

![](/images/dc-bot/01/10.jpg)

-------

這是本次實作的[範例程式碼](https://github.com/B-l-u-e-b-e-r-r-y/Discord-Bot-01)，可以參考檔案的配置，或是直接 Clone 到自己的電腦裡運行。

> **啟用步驟**
> 1. 安裝：`npm install`
> 2. 到 token.json，將 Your token 更換自己機器人的 Token
> 3. 執行：`node discord.js`

------------------------------------------

**【用 JS 寫一個 Discord Bot！】系列文章**

[【用 JS 寫一個 Discord Bot！】01 建立機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot01/)
[【用 JS 寫一個 Discord Bot！】02 音樂機器人](https://b-l-u-e-b-e-r-r-y.github.io/post/DiscordBot02/)