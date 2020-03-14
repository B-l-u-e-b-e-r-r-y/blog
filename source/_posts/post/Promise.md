---
title: Javascript ES6 Promise
comments: true
date: 2020-03-14 00:00:00
tags: 
- ES6
- Javascript
- w3HexSchool
categories: 
- Javascript
---
Promise 是很適合用來處理非同步的方法，大多數情況是用來處理非同步事件或 Callback hell(回調地獄)。

## 非同步調用
例如 `getData()` 在 `api()` 還沒回傳值就想取得結果的話，就會出現 `undefined`。可以看看下面的例子：
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

`setTimeout` 是 Javascript 中一種非同步的方法，它會等到指定時間過後才會執行裡面的程式碼，但與此同時，其他的程式碼一樣會繼續進行。

## 使用 Promise 解決非同步調用
那麼該如何解決呢？這時候就可以使用 Promise。
要建立 Promise 必須使用以下的寫法，回傳 resolve(解決) 及 reject(拒絕)，當然只回傳 resolve 或 reject 也是可以的，它們類似於 `return` 的概念，寫在它們後面的程式碼不會被執行。
```js
const promise = new Promise((resolve, reject) => {
    if (/* 任何條件 */) {
        resolve('Success');
    } else {
        reject('Fail');
    }
});
```
定義 Promise 之後就可以呼叫它，寫法是這樣的 `promise.then(successCallback, failureCallback)`，如果剛剛回傳的結果是 resolve(解決)，則調用 `successCallback`，反之如果是回傳 reject(拒絕)，則調用 `failureCallback`。
這邊特別提一下，`failureCallback` 是可選的，不一定要使用它來處理錯誤，使用 `catch(failureCallback)` 也有一樣的效果，但這兩種寫法在其他情境下會產生差異，後面**錯誤處理**的部分會提到。
下面這段程式碼會在 Promise 回傳 resolve 或 reject 之後才被執行，因此可以達到同步延遲的效果。
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
        console.log(userData);  // Object { user: [] }
        countryApi((countryData) => {
            console.log(countryData);  // Object { country: [] }
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
    userApi()
    .then((userData) => {
        console.log(userData);  // Object { user: [] }
        return countryApi();  // 執行 countryApi()，因為函式回傳的是 Promise 物件，所以可以繼續用 then 串接
    })
    .then((countryData) => {
        console.log(countryData);  // Object { country: [] }
        return itemApi();
    })
    .then((itemData) => {
        console.log(itemData);  // Object { item: [] }
    })
    // 錯誤處理↓
    .catch((e) => {
        console.log(e);
    });
}

getData();
```
Promise 可以使用串連的寫法，讓程式碼又更具可讀性，錯誤處理的寫法直觀又有規範，好 Promise 不用嗎。

## 錯誤處理
錯誤處理主要有兩種寫法，分別為：
* `new Promise.then(successCallback, failureCallback)`
* `new Promise.then(successCallback).catch(failureCallback)`
那它們之間究竟有什麼不同呢？
我把中間的 countryApi 改成回傳 reject，看看不同的錯誤處理方式如何運行。

### new Promise.then(successCallback, failureCallback)
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
也因為執行的是 failureCallback，failureCallback 的程式除了 `console.log(err)` 以外並沒有做任何事情，故而下一個 then 的 `console.log(itemData)` 就會出現 `undefined`。
如果這個錯誤處理改寫成 `(err) => { return itemApi() }` 結果就不會是 `undefined` 了。
這個寫法可以避免中間有一個 api 出錯，後面就不會繼續執行的問題。

### new Promise.then(successCallback).catch(failureCallback)
這個寫法是將錯誤統一交給 catch 處理：
```js
const getData = () => {
    userApi()
    .then((userData) => {
        console.log(userData);
        return countryApi();
    })
    .then((countryData) => {
        console.log(countryData);
        return itemApi();
    })
    .then((itemData) => {
        console.log(itemData);
    })
    .catch((e) => {
        console.log(e);
    });
}

// 結果：
// Object {
//    user: []
// }
// "Error!"
```

疑？為什麼 `itemApi` 的部分沒有執行？
是因為只要某部分回傳了 reject，就會進到 catch 的部分，中間的過程會直接跳過。
但是如果在 catch 後面寫串連，還是可以繼續執行，只是一般很少人這樣使用：
```js
const getData = () => {
    userApi()
    .then((userData) => {
        console.log(userData);
        return countryApi();
    })
    .then((countryData) => {
        console.log(countryData);
        return itemApi();
    })
    .then((itemData) => {
        console.log(itemData);
    })
    .catch((e) => {
        console.log(e);
    })
    .then(() => {
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
