---
title: Promise chain 鏈| 為什麼會有兩次then?
date: 2023-04-21 11:08:03
tags:
- Promise
- JavaScript
categories: JavaScript
---
## 前言
在看MDN fetch文件時，

[https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API/Using_Fetch#使用_fetch_發送請求_request](https://developer.mozilla.org/zh-TW/docs/Web/API/Fetch_API/Using_Fetch#%E4%BD%BF%E7%94%A8_fetch_%E7%99%BC%E9%80%81%E8%AB%8B%E6%B1%82_request)

看到以下code:

```jsx
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

想說奇怪，在工作上都是接一個then，沒看過接兩個的。

研究一下，這與Javascript ES6 fetch函數有關。

第一個then代入的resolve函數有`return response.json()`，而`response.json()`返回一個Promise，這個Promise的executor做的是把傳輸資料解析成JSON，代表轉化成JSON的過程為一個非同步操作，所以可以再調用then，使用已經成功轉化成JSON的資料做處理。

因為工作上只用過axios套件，axios只需一次then就能取用JSON資料，這也是axios方便的地方。

## 加深認識then

### 定義

回傳一個**Promise**物件，接收兩參數，Promise成功和失敗的回調函數(handler)。

```jsx
p.then(onFulfilled[, onRejected]);

p.then(function(value) {
  // fulfillment
}, function(reason) {
  // rejection
});
```

### 回傳值

回傳一個Promise，它的狀態是根據handler回傳的值:

1. 回傳一個值，則 `then` 回傳一個 已實現(resolved)的promise
2. 拋出一個例外，則 `then` 回傳一個 已拒絕(rejected)的promise
3. 回傳一個已實現的Promise，則 `then` 回傳一個 已實現(resolved)的promise
4. 回傳一個已拒絕的Promise，則 `then` 回傳一個 已拒絕(resolved)的promise
5. 回傳一個擱置(pending)的Promise，則 `then` 回傳一個Promise，成功與否由隨後的handler決定，如上面的範例
6. 沒有回傳值，則 `then` 回傳一個已實現(resolved)的Promise，但Promise的value(property)是`undefined`

最後一點，MDN沒有，自己測的，用Chrome印出的console如下:

![Untitled](Untitled.png)

### 小結

1. 因為then會回傳Promise，所以才有Promise鏈的發生(第五點)
2. 我自己開發handler沒有回傳過Promise，通常都沒有回傳東西，因為不需要對拿到的值做非同步處理。
3. 第三、四點還有一點疑問，要怎麼寫回傳已實現或拒絕的Promise?
例如:
`return P1`
P1是一個已實現的Promise
4. MDN文件中這個code可以自己去跑看看
    
    ```jsx
    var resolvedProm = Promise.resolve(33);
    
    var thenProm = resolvedProm.then(function(value){
        console.log("我在 main stack 之後被呼叫。收到及將回傳的值為：" + value);
        return value;  // 可以試看看不要return 東西(第六點)
    });
    // 立即紀錄 thenProm
    console.log(thenProm);
    
    // 我們可以使用 setTimeout 以延遲（postpone）函式執行直到堆疊為空
    setTimeout(function(){
        console.log(thenProm);
    });
    ```
    

### 更新

上述第三點，Promise有內建方法可以回傳已實現的Promise

`[**Promise.resolve**](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve#%E4%BD%BF%E7%94%A8_promise.resolve_%E9%9D%9C%E6%85%8B%E6%96%B9%E6%B3%95)`

```jsx
Promise.resolve('Success').then(function(value) {
  console.log(value); // "Success"
}, function(value) {
  // not called
});
```

如果要回傳rejected狀態的Promsie

`[Promise.reject()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)`

所以在then所回調的函數裡面，可以這樣寫

`return Promise.resolve("Success")`

## 參考資料

https://blog.csdn.net/xiaoyuer_2020/article/details/112309991

https://developer.mozilla.org/en-US/docs/Web/API/Response/json

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/then

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve