---
title: Javascript ES6 Promise
comments: true
banner_img: /images/promise/banner.jpeg
index_img: /images/promise/banner.jpeg
date: 2020-03-14 00:00:00
tags: 
- ES6
- Javascript
- w3HexSchool
categories: 
- Javascript
---

Promise 是很適合用來處理非同步的方法，大多數情況是用來處理非同步事件或 Callback hell (回調地獄)。

## 非同步調用

例如 `getData()` 在 `api()` 還沒回傳值就想取得結果，就會出現 `undefined`。可以看看下面的例子：

```js
const api = () => {
    // 模擬等待 api 回傳的時間
    setTimeout(() => {
        return { value: 1 };
    }, 2000);
}

const getData = () => {
    const data = api();
    console.log(data);  // undefined
}

getData();
```

`setTimeout` 是 Javascript 中一種非同步的方法，它會等到指定時間過後才會執行裡面的程式碼，但與此同時，其他的程式碼一樣會繼續進行，**不會等到 `setTimeout` 執行結束才繼續往下**。

## 使用 Promise 解決非同步調用

那麼該如何解決呢？這時候就可以使用 Promise。

建立 Promise 必須回傳 resolve(解決) 及 reject(拒絕)，當然只回傳 resolve 或 reject 也是可以的。

```js
const promise = new Promise((resolve, reject) => {
    if (/* 任何條件 */) {
        resolve('Success');
    } else {
        reject('Fail');
    }
});
```
定義 Promise 之後就可以呼叫它：
`promise.then(fulfilledCallback, rejectedCallback)`

如果剛剛回傳的結果是 resolve(解決)，則執行 `fulfilledCallback`，反之如果是回傳 reject(拒絕)，則執行 `rejectedCallback`。

**※ 這邊特別提一下，`rejectedCallback` 是可選的，不一定要使用它來處理錯誤，使用 `catch(rejectedCallback)` 也有一樣的效果，但這兩種寫法在其他情境下會產生差異，後面錯誤處理的部分會提到。**

下面這段程式碼會在 Promise 回傳 resolve 或 reject 之後才被執行，因此可以達到同步延遲的效果：

```js
promise.then((res) => {
    console.log(res);  // Success
}, (err) => {
    console.log(err);  // Fail
});

// 或
promise.then((res) => {
    console.log(res);  // Success
}).catch((err) => {
    console.log(err);  // Fail
});
```

學會 Promise 的基本用法後，就可以回到最上面的問題，如何等到 `api()` 回傳結果再取值呢？

改寫一下程式，在 `api()` 加入 Promise，並於 `getData()` 調用：

```js
const api = () => {
    // 模擬等待 api 回傳的時間
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ value: 1 });
        }, 2000);
    });
}

const getData = () => {
    let data;
    api().then((result) => {
        data = result;
        console.log(data);  // Object { value: 1 }
    });
}

getData();
```

這樣就可以解決非同步調用的問題了。

## Callback hell

接下來看看 Callback hell 的問題，下面的例子是模擬取用 user、country、item 這三個不同的 api，而且必須取完 userApi 再取 countryApi，然後再取 itemApi：

```js
const userApi = (callback) => {
    setTimeout(() => {
        callback({ user: [] });
    }, 1000);
}

const countryApi = (callback) => {
    setTimeout(() => {
        callback({ country: [] });
    }, 1000);
}

const itemApi = (callback) => {
    setTimeout(() => {
        callback({ item: [] });
    }, 1000);
}

const getData = () => {
    userApi((userData) => {
        console.log(userData);          // Object { user: [] }
        countryApi((countryData) => {
            console.log(countryData);   // Object { country: [] }
            itemApi((itemData) => {
                console.log(itemData);  // Object { item: [] }
            });
        });
    });
}

getData();
```

這樣的寫法非常難讀和維護，如果再加入其他動作或錯誤處理，那真的是一坨超級難讀的程式碼了。

## 使用 Promise 解決 Callback hell

將程式碼改寫如下：

```js
const userApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ user: [] });
        }, 1000);
    });
}

const countryApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ country: [] });
        }, 1000);
    });
}

const itemApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ item: [] });
        }, 1000);
    });
}

const getData = () => {
    userApi().then((userData) => {
        console.log(userData);     // Object { user: [] }
        return countryApi();       // 執行 countryApi()，因為函式回傳的是 Promise 物件，所以可以繼續用 then 串接
    }).then((countryData) => {
        console.log(countryData);  // Object { country: [] }
        return itemApi();
    }).then((itemData) => {
        console.log(itemData);     // Object { item: [] }
    }).catch((e) => {              // 錯誤處理
        console.log(e);
    });
}

getData();
```

Promise 可以使用串連的寫法，讓程式碼又更具可讀性，錯誤處理的寫法直觀又有規範，好 Promise 不用嗎。

## 錯誤處理

錯誤處理主要有兩種寫法，分別為：

* `new Promise.then(fulfilledCallback, rejectedCallback)`
* `new Promise.then(fulfilledCallback).catch(rejectedCallback)`

那它們之間究竟有什麼不同呢？

我把中間的 countryApi 改成回傳 reject，看看不同的錯誤處理方式如何運行。

### new Promise.then(fulfilledCallback, rejectedCallback)

```js
const userApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ user: [] });
        }, 1000);
    });
}

const countryApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            // 把這個 api 的回傳改成 reject
            reject('Error!');
        }, 1000);
    });
}

const itemApi = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve({ item: [] });
        }, 1000);
    });
}

/**
 * 因為每一個 Promise 都有錯誤處理了，所以不寫 catch
 * 要寫 catch 也是可以的，這樣的話這個 catch 主要會抓到這邊 callback 的錯誤，而不是 reject 回傳的內容
 */
const getData = () => {
    userApi()
    .then((userData) => {
        console.log(userData);
        return countryApi();
    }, (err) => {
        console.log(err);
    })
    .then((countryData) => {
        console.log(countryData);
        return itemApi();
    }, (err) => {
        console.log(err);
    })
    .then((itemData) => {
        console.log(itemData);
    }, (err) => {
        console.log(err);
    });
}

getData();

// 結果：
// Object {
//    user: []
// }
// "Error!"
// undefined
```

這個範例成功取得第一個 userApi，之後在取得 countryApi 時出現錯誤，因此回傳 `"Error!"`。

那最後一行的 `undefined` 是怎麼回事？

是因為這種寫法的錯誤處理執行完後，還會繼續執行接下來的程式 `.then()` 的緣故。

也因為執行的是 rejectedCallback，rejectedCallback 的程式除了 `console.log(err)` 以外並沒有做任何事情，故而下一個 then 的 `console.log(itemData)` 就會出現 `undefined`。

**這個寫法可以避免中間有一個 api 出錯，後面就不會繼續執行的問題。**

### new Promise.then(fulfilledCallback).catch(rejectedCallback)

這個寫法是將錯誤統一交給 catch 處理：

```js
const getData = () => {
    userApi().then((userData) => {
        console.log(userData);
        return countryApi();
    }).then((countryData) => {
        console.log(countryData);
        return itemApi();
    }).then((itemData) => {
        console.log(itemData);
    }).catch((e) => {
        console.log(e);
    });
}

// 結果：
// Object {
//    user: []
// }
// "Error!"
```

咦？為什麼只到 `"Error!"` 就沒了？`itemApi` 的部分沒有執行？

**這是因為只要某部分回傳了 reject，就會進到 catch 的部分，中間的過程會直接跳過。**

但是如果在 catch 後面寫串連，還是可以繼續執行，只是一般很少人這樣使用：

```js
const getData = () => {
    userApi().then((userData) => {
        console.log(userData);
        return countryApi();
    }).then((countryData) => {
        console.log(countryData);
        return itemApi();
    }).then((itemData) => {
        console.log(itemData);
    }).catch((e) => {
        console.log(e);
    }).then(() => {
        console.log('Keep going.');
    });
}

// 結果：
// Object {
//    user: []
// }
// "Error!"
// "Keep going."
```

## finally

還有一個方法是 `finally()`，它會在執行完 `then()` 和 `catch()` 後執行，確保無論是 fulfilled 或 rejected 都會執行某些程式碼的一種方法。

```js
Promise.resolve(1).then((value) => {
    console.log(value);
}).catch((err) => {
    console.log(err);
}).finally(() => {
    console.log('finally');
});

// 1
// finally
```

## 其他寫法

將 Promise 展開後可以看見下面的結構：

![](/images/promise/04.jpg)

從這張圖中可以看見幾種 Promise 可用的函式，分別為：

* Promise.resolve
* Promise.reject
* Promise.all
* Promise.race
* Promise.allSettled

### Promise.resolve

`Promise.resolve(value)`

可以直接回傳一個 resolve 的 Promise。

### Promise.reject

`Promise.reject(value)`

可以直接回傳一個 reject 的 Promise。

### Promise.all

`Promise.all(array).then(fulfilledCallback, rejectedCallback)`

完成全部的 Promise 後才會執行。

`Promise.all` 函式需要放入陣列，陣列內容必須是 Promise。

如果 Promise 全部回傳 resolve，會執行 `fulfilledCallback`，回傳全部 resolve 值，並組成一個陣列。

反之，如果有任何一個回傳 reject 則執行 `rejectedCallback`，回傳第一個 reject 值。

```js
Promise.all([
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3)
]).then((array) => {
    console.log(array);
}, (err) => {
    console.log(err);
});

// [1, 2, 3]
```

### Promise.race

`Promise.race(array).then(fulfilledCallback, rejectedCallback)`

與 `Promise.all` 的寫法相同，但是它只接收第一個回傳的 Promise（不論 resolve 或 reject），有點類似賽跑，比賽誰先到終點。

```js
Promise.all([
    Promise.reject(1),
    Promise.resolve(2),
    Promise.resolve(3)
]).then((resolve) => {
    console.log('resolve: ', resolve);
}, (reject) => {
    console.log('reject: ', reject);
});

// reject: 1
```

### Promise.allSettled

`Promise.allSettled(array).then(callback))`

`Promise.allSettled` 只有在全部的 Promise 都完成後才會執行。

它會回傳一個陣列，裡面包含：

```js
// resolve 時回傳
{
    status: "fulfilled",
    value: "resolve value"
}

// reject 時回傳
{
    status: "rejected",
    reason: "reject value"
}
```

寫法與 `Promise.all` 及 `Promise.race` 大致相同，只是它不需要第二個 callback：

```js
Promise.allSettled([
    Promise.resolve(1),
    Promise.reject(2)
]).then((array) => {
    console.log(array);
});

/**
 *  [
 *      { status: "fulfilled", value: 1 }
 *      { status: "rejected", reason: 2 }
 *  ]
 */
```

## Promise 的當前值與狀態

當前值與狀態分別指的是 `[[PromiseValue]]` 與 `[[PromiseStatus]]`。

`[[PromiseValue]]` 指的是 resolve 或 reject 回傳的值。

`[[PromiseStatus]]` 分為以下幾種狀態：

* resolved：表示成功
* rejected：表示失敗
* pending：表示尚未回傳 resolve 或 reject，`[[PromiseValue]]` 會被指定為 `undefined`