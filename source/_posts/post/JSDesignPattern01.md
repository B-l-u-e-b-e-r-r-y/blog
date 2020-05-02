---
title: Javascript 設計模式 - 單例模式
comments: true
banner_img: /images/js_banner.jpeg
index_img: /images/design-pattern/01.png
date: 2020-04-18 20:53:00
tags: 
- Design Pattern
- Javascript
- w3HexSchool
categories: 
- Javascript
- Design Pattern
---

# 前言

本篇內容是我閱讀《[JavaScript設計模式與開發實踐](https://www.books.com.tw/products/0010687594)》的筆記，這些程式碼會牽涉到一些觀念，如閉包、物件導向、原型鍊等，如果有不懂的地方可以詢問 Google 大神，這邊會比較著重在講解與實作設計模式的部分。

# 單例模式

單例模式是確保物件只建立一次的設計模式，好處是能減少不必要的內存，它的原則是**保證一個類別僅有一個實例，並提供一個存取它的全域存取點**，透過一個變數來記錄類別有沒有建立過物件，如果沒有被建立過，則建立物件並且返回；如果已建立過，則返回已建立的物件。

以前端開發者的角度來看，它適合用來實作按鈕點擊後的彈出式視窗，不論點擊多少次按鈕，這個彈出式視窗永遠只會被建立一次。

## 不透明的單例模式

### ES5

```js
const Singleton = function(name) {
    this.name = name;
    this.instance = null;
}

Singleton.getInstance = function(name) {
    if (!this.instance) {
        this.instance = new Singleton(name);
    }
    return this.instance;
}
```

### ES6

```js
class Singleton {
    constructor(name) {
        this.name = name;
    }
        
    static getInstance(name) {
        if (!this.instance) {
            this.instance = new Singleton(name);
        }
        return this.instance;
    }
}
```

如果要驗證這個單例有沒有成功，可以這樣寫：

```js
const a = Singleton.getInstance('a');
const b = Singleton.getInstance('b');
console.log(a === b);  // true
```

不同於以往的 `new XXX()` 方式，上面的例子透過 `Singleton.getInstance` 來獲得 Singleton 類別的唯一物件，但這樣的寫法增加了這個類別的「不透明性」，使用者必須知道這是一個單例類別才行。

## 透明的單例模式

一個透明的單例，使用者可以像使用其他任何普通類別一樣，能透過 `new XXX()` 來獲得物件。下面的例子使用 CreateDiv 的單例類別來建立唯一的 div 節點，這個例子使用閉包來保存 `instance` 變數。

### ES5

```js
const CreateDiv = (function() {
    let instance;
    const createDiv = function(html) {
        if (instance) {
            return instance;
        }
        
        this.html = html;
        this.init();
        return instance = this;
    }
        
    createDiv.prototype.init = function() {
        const div = document.createElement('div');
        div.innerHTML = this.html;
        document.body.appendChild(div);
    }
        
    return createDiv;
})();

const a = new CreateDiv('a');
const b = new CreateDiv('b');
console.log(a === b);  // true
```

### ES6

```js
class CreateDiv {
    constructor(html) {
        if (!CreateDiv.instance) {
            this.html = html;
            this.init();
            CreateDiv.instance = this;
        }

        return CreateDiv.instance;
    }
        
    init() {
        const div = document.createElement('div');
        div.innerHTML = this.html;
        document.body.appendChild(div);
    }
}

const a = new CreateDiv('a');
const b = new CreateDiv('b');
console.log(a === b);  // true
```

這段程式碼其實沒有什麼問題，但在 createDiv 這個匿名函數裡同時做了兩件事：管理單例及建立 div，根據[單一職責原則](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%B8%80%E5%8A%9F%E8%83%BD%E5%8E%9F%E5%88%99)這樣的代碼應該盡量避免。所以後面會使用[代理模式](https://zh.wikipedia.org/wiki/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F)來改善這段代碼。

## 用代理模式實作單例

這裡把管理單例的邏輯移到代理類別 proxySingleCreateDiv，和建立 div 的邏輯分開，這樣的代碼符合了單一職責原則，也更具可讀性。

### ES5
```js
const CreateDiv = function(html) {
    this.html = html;
    this.init();
}

CreateDiv.prototype.init = function() {
    const div = document.createElement('div');
    div.innerHTML = this.html;
    document.body.appendChild(div);
}

const proxySingleCreateDiv = (function() {
    let instance;
    return function(html) {
        if (!instance) {
            instance = new CreateDiv(html);
        }
        return instance;
    }
})();

const a = new proxySingleCreateDiv('a');
const b = new proxySingleCreateDiv('b');
console.log(a === b);  // true
```

### ES6

```js
class CreateDiv {
    constructor(html) {
        this.html = html;
        this.init();
    }
        
    init() {
        const div = document.createElement('div');
        div.innerHTML = this.html;
        document.body.appendChild(div);
    }
}

class proxySingleCreateDiv {
    constructor(html) {
        if (!proxySingleCreateDiv.instance) {
            proxySingleCreateDiv.instance = new CreateDiv(html);
        }
        return proxySingleCreateDiv.instance;
    }
}

const a = new proxySingleCreateDiv('a');
const b = new proxySingleCreateDiv('b');
console.log(a === b);  // true
```

## 惰性單例

惰性單例是指在需要時才建立物件，[這個例子](#不透明的單例模式)其實就是使用惰性單例，當我們呼叫 `Singleton.getInstance()` 才建立單例，而非頁面載入時就建立好單例。

接著我們會使用全域變數結合惰性單例，實作彈出式視窗。

# JavaScript 的單例模式

Javascript 其實是一門無類別的語言，所以基於「類別」的傳統單例模式並不適用，因此開發者常會將全域變數作為單例使用，例如：

```js
const a = {};
```

它能滿足單例的兩個條件：確保只有一個實例，又能提供全域存取。但使用全域變數容易造成命名空間汙染，也可能在開發的過程中不小心被覆蓋。要解決這個問題，可以使用命名空間或閉包封裝私有變數。

### 命名空間
使用命名空間的好處是能降低全域變數的需求量，將變數都宣告在命名空間裡，避免命名衝突。要建立命名空間，可以直接使用物件字面常數建立，或動態建立命名空間。

#### 物件字面常數
```js
const namespace = {
    a: function() {
        // do something
    },
    b: function() {
        // do something
    }
}
```

#### 動態建立命名空間

```js
const app = {};
app.namespace = function(name) {
    const key = name.split('.');
    let _app = app;
    for (let i in key) {
        if ( !_app[key[i]] ) {
            _app[key[i]] = {};
        }
        
        _app = _app[key[i]];
    }
}

app.namespace('event');
app.namespace('dom.style');

console.log(app);
/**
 *  const app = {
 *      event: {},
 *      dom: {
 *          style: {}
 *      }
 *  }
 */
```

### 閉包封裝私有變數

```js
const user = (function() {
    const _name = 'Blueberry';
    const _age = 24;
    return {
        getUserInfo: function() {
            return _name + '-' + _age;
        }
    }
})();

console.log(user.getUserInfo());  // Blueberry 24
```

## 實作彈出式視窗

下面我們會實作一個彈出式視窗的惰性單例，按下「登入」按鈕時，彈出式視窗才會被建立。

### html

```html
<button id="loginBtn">登入</button>
```

### css

```css
.login {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    padding: 20px 30px;
    border: solid 1px black;
}
```

### Javascript

```js
const createLoginLayer = (function() {
    let div;
    return function() {
        if (!div) {
            div = document.createElement('div');
            div.innerHTML = '我是彈出式視窗';
            div.className = 'login';
            div.style.display = 'none';
            document.body.appendChild(div);
        }

        return div;
    }
})();

document.getElementById('loginBtn').onclick = function() {
    const loginLayer = createLoginLayer();
    loginLayer.style.display = 'block';
};
```

但是這個惰性單例還不夠好，`createLoginLayer` 物件違反了單一職責原則，它同時做了管理單例和建立彈出式視窗兩個動作。如果下次要建立的不是 div，而是一個 button，這段程式碼又要再重新抄一遍：

```js
const createButton = (function() {
    let button;
    return function() {
        if (!button) {
            button = document.createElement('button');
            button.innerHTML = '我是按鈕';
            button.style.display = 'none';
            document.body.appendChild(button);
        }
        return button;
    }
})();
```

所以我們將管理單例和建立彈出式視窗的邏輯分開，將管理單例的邏輯移到 `getSingle`，`createLoginLayer` 單純放建立視窗的邏輯就好。

```js
const createLoginLayer = function() {
    const div = document.createElement('div');
    div.innerHTML = '我是彈出式視窗';
    div.className = 'login';
    div.style.display = 'none';
    document.body.appendChild(div);
    return div;
};

const getSingle = function(fn) {
    let result;
    return function() {
        if (!result) {
            result = fn.apply(this);
        }
        return result;
    }
}

const createSingleLoginLayer = getSingle(createLoginLayer);

document.getElementById('loginBtn').onclick = function() {
    const loginLayer = createSingleLoginLayer();
    loginLayer.style.display = 'block';
};
```

如此一來，要把建立彈出式視窗換成建立按鈕就簡單多了，不必動到管理單例的部分。

```js
const createButton = function() {
    const button = document.createElement('button');
    button.innerHTML = '我是按鈕';
    button.style.display = 'none';
    document.body.appendChild(button);
    return button;
};

const getSingle = function(fn) {
    let result;
    return function() {
        if (!result) {
            result = fn.apply(this);
        }
        return result;
    }
}

const createSingleButton = getSingle(createButton);

document.getElementById('loginBtn').onclick = function() {
    const buttonLayer = createSingleButton();
    buttonLayer.style.display = 'block';
};
```

---------------------------------------

參考資料：
[JavaScript設計模式與開發實踐](https://www.books.com.tw/products/0010687594)