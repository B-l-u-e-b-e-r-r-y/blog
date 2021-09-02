---
title: React Children 屬性
date: 2020-02-05
comments: true
categories: 
- React
---
# React Children 屬性

使用組件時，大多數都是這樣寫：

```js
<Component />
```

但我們其實可以在組件中寫入其他內容：

```js
<Component>
    <p>Hello World!</p>
</Component>
```

除了在組件中寫入內容以外，組件本身也要加入 `props.children` 的屬性才會出現內容，如果沒有寫入任何內容，則 `props.children` 返回 null。

> App.js

```js
import React, { Component } from 'react';
import Card from './Card';

class App extends Component {
    render() {
        return (
            <Card>
                <h1>Hello World!</h1>
            </Card>
        );
    }
}

export default App;
```

> Card.js

```js
const Card = (props) => {
    return (
        <div>
            {props.children}
        </div>
    );
}

export default Card;
```

網頁呈現：
![](/images/react-children/1.png)

---------------------------------------

## 陣列處理

### React.Children.map

除了文字以外，children 也可以傳遞陣列，這邊我們使用 React 提供的方法 `React.Children.map` 來處理資料。

> App.js

```js
class App extends Component {
    render() {
        return (
            <Card>
                {
                    function printData() {
                        return ['Apple', 'Orange', 'Banana'];
                    }()
                }
            </Card>
        );
    }
}
```

> Card.js

```js
const Card = (props) => {
    return (
        <div>
            {
                React.Children.map(props.children,
                    (child, i) => {
                        return <h1>{child}</h1>;
                    }
                )
            }
        </div>
    );
}
```

網頁呈現：

![](/images/react-children/2.png)

### React.Children.forEach

與 `React.Children.map` 的使用方式相同，但是不會回傳陣列。

---------------------------------------

## Child 數量計算

### React.Children.count

這個方法用來計算 child 的數量，舉例來說：

```js
<Component>
    <p>Hello World!</p>
    <p>Hello World!</p>
    <p>Hello World!</p>
</Component>
```

裡面包含了三個 `<p>Hello World!</p>`，所以數量 = 3。

> App.js

```js
class App extends Component {
    render() {
        return (
            <Card>
                <h1>我是第一個child</h1>
                <h1>我是第二個child</h1>
                <h1>我是第三個child</h1>
            </Card>
        );
    }
}
```

> Card.js

```js
const Card = (props) => {
    return (
        <div>
            child 數量：{ React.Children.count(props.children) }
        </div>
    );
}
```

網頁呈現：
![](/images/react-children/3.png)

---------------------------------------

## 只呈現一個 Child

### React.Children.only

這個方法是讓組件只呈現一個 child，如果 children 不只一個 child 將會報錯：

![](/images/react-children/4.png)

> App.js

```js
class App extends Component {
    render() {
        return (
            <Card>
                <h1>我是唯一的child</h1>
            </Card>
        );
    }
}
```

> Card.js

```js
const Card = (props) => {
    return (
        <div>
            { React.Children.only(props.children) }
        </div>
    );
}
```

網頁呈現：
![](/images/react-children/5.png)

---------------------------------------

## 將 Child 轉成陣列

### React.Children.toArray

`React.Children.toArray` 方法會將 child 轉成一個扁平的陣列，並對每個 child 指定一個 key。

下面示範用這個方法將每個 child 數字以小到大排序：

> App.js

```js
class App extends Component {
    render() {
        return (
            <Card>
                <h1>196</h1>
                <h1>165</h1>
                <h1>178</h1>
            </Card>
        );
    }
}
```

> Card.js

```js
const Card = (props) => {
    let child = React.Children.toArray(props.children);
    child = child.sort((a, b) => {
        return a.props.children > b.props.children ? 1 : -1;
    });

    return (
        <div>
            {child}
        </div>
    );
}
```

網頁呈現：
![](/images/react-children/6.png)