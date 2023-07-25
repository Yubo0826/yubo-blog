---
title: 認識 JS Promise
date: 2023-04-16 11:13:03
tags: 
- Promise
- JavaScript
categories: JavaScript
---
## C**onstructor**

```jsx
const promise1 = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('foo');
  }, 300);
});

promise1.then((value) => {
  console.log(value);
  // Expected output: "foo"
});

console.log(promise1);
// Expected output: [object Promise]
```

## Syntax

```jsx
new Promise(executor) 

// or 可以表示

new Promise( /* executor */ function(resolve, reject) { ... } );
```

## Parameter

`executor`

為一個function，接收兩個參數(function)，`resovle`及`rejects`，通常executor會發起一些非同步操作，接著，成功完成後會執行`resolve`；如果錯誤，執行`rejects`。

![Untitled](1.png)

## 為什麼要使用Promise

有個常見的需求是依序呼叫兩個以上的非同步函數，因為多數非同步函數都有callback，如果非同步函數一個接一個，那就會很恐怖。

主要原因是避免波動拳(Pyramid of Doom)

![Untitled](2.png)

```jsx
doSomething(function(result) {
  doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log('Got the final result: ' + finalResult);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);
```

但如果用Promise

```jsx
doSomething().then(function(result) {
  return doSomethingElse(result);
})
.then(function(newResult) {
  return doThirdThing(newResult);
})
.then(function(finalResult) {
  console.log('Got the final result: ' + finalResult);
})
.catch(failureCallback);
```

看起來就會簡潔多了

## 基本規則

1. 一個Promise物件處於3種狀態
- pending (擱置)
- fulfilled (實現)
- rejected (拒絕)

1. Promise狀態只能從pending → fulfilled ，或是pending → rejected 
2. 一旦狀態確定，就不能再更改
3. 當狀態發生轉變，就會調用透過then方法傳入的function

## then

Promise物件有then方法，then是定義在原型物件Promise.prototype上的，他的作用是當Promise狀態改變時的回調函數(callback)，當非同步操作成功時，執行resolve，失敗則執行rejected。

其他Promise原型定義方法 👇

[https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise#promise_原型](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise#promise_%E5%8E%9F%E5%9E%8B)

## 實際範例

Ajax+promise的應用:

```jsx
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};
```

## 參考資料

https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Promise/then

https://juejin.cn/post/6936886197817966606

https://github.com/YvetteLau/Blog/issues/2