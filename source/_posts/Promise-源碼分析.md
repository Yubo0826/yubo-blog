---
title: Promise 源碼分析
date: 2023-04-25 11:16:59
tags:
- Promise
- JavaScript
categories: JavaScript
---
源碼網址:

https://github.com/YvetteLau/Blog/issues/2

## Promise建構子函數

### Property

`status`

Promise的狀態，初始為`'pending'`

非同步操作成功後為`'fulfilled'`，失敗為`'rejected'`

`resolve`

成功後調用的函數

接收一個參數value，

把`status`改為`'fulfilled'`

`reject`

失敗後調用的函數

接收一個參數value，定義為Promise的property

把`status`改為`'rejected'`

`onFulfilled`

為一陣列，元素是函數

放進等待執行的回調函數(成功)

`onRejected`

為一陣列，元素是函數

放進等待執行的回調函數(失敗)

## `Promise.prototype.then`定義

接收兩個參數，皆為函數，一個成功時的回調(`onFulfilled`)，一個失敗時的回調(`onRejected`)

返回一個Promise(範例變數叫`promise2`)

Promise的非同步操作是，先判定status的狀態

1. 當`status === FULFILLED`
    
    呼叫成功時的回調函數，引入value當參數，return value賦值給x
    
    然後執行`resolvePromise(promise2, x, resolve, reject)`
    

1. 當`status ===``REJECTED`
    
    呼叫成功時的回調函數，引入reason當參數，return reason賦值給x
    
    然後執行`resolvePromise(promise2, x, resolve, reject)`
    

1. 當`status === PENDING`
    
    property`onFulfilled`陣列push進一個非同步操作
    
    為上面第一點內容
    
    property`onRejected`陣列push進一個非同步操作
    
    為上面第二點內容
    

🔎 註一

程式碼剛開始執行時，由於非同步函數還沒開始，Promise狀態是pending，所以一定都是先跑到3，想不太到跑到1、2的情況

## `resolvePromise` 定義

如同函數名，旨在處理成功時的回調(`onFulfilled`)所回傳的Promise

`resolvePromise(promise2, x, resolve, reject)`

接收四個參數，分別為

1. then()返回的Promise
2. 成功or失敗回調函數的回傳值
3. 1的Promise resolve
4. 1的Promise reject

先判斷`promise2 === x`

為真的話，執行`reject(new TypeError('Chaining cycle'))`

後面之後再補看

先引用來源的說明

簡單來說要處理Promise chain的產生

![Untitled](1.png)

## 模擬執行順序測試

### 測試1

![Untitled](2.png)

`a.then`是同步的，在非同步操作之前，過1000ms後，123出現

### 測試2

在then定義裡面，在實現、拒絕、擱置三種情況的區塊裡面，加入console.log，看看程式碼執行時怎麼跑的

```jsx
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';
function Prom(executor) {
    let self = this;
    self.status = PENDING;
    self.onFulfilled = [];//成功的回调
    self.onRejected = []; //失败的回调
    //PromiseA+ 2.1
    function resolve(value) {
        if (self.status === PENDING) {
            self.status = FULFILLED;
            self.value = value;
            self.onFulfilled.forEach(fn => fn());//PromiseA+ 2.2.6.1
        }
    }

    function reject(reason) {
        if (self.status === PENDING) {
            self.status = REJECTED;
            self.reason = reason;
            self.onRejected.forEach(fn => fn());//PromiseA+ 2.2.6.2
        }
    }

    try {
        executor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}

Prom.prototype.then = function (onFulfilled, onRejected) {
    //PromiseA+ 2.2.1 / PromiseA+ 2.2.5 / PromiseA+ 2.2.7.3 / PromiseA+ 2.2.7.4
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason };
    let self = this;
    //PromiseA+ 2.2.7
    let promise2 = new Prom((resolve, reject) => {
        if (self.status === FULFILLED) {
            //PromiseA+ 2.2.2
            //PromiseA+ 2.2.4 --- setTimeout
            console.log('then裡面 FULFILLED')
            setTimeout(() => {
                try {
                    //PromiseA+ 2.2.7.1
                    let x = onFulfilled(self.value);
                    resolvePromise(promise2, x, resolve, reject);
                } catch (e) {
                    //PromiseA+ 2.2.7.2
                    reject(e);
                }
            });
        } else if (self.status === REJECTED) {
            console.log('then裡面 REJECTED')
            //PromiseA+ 2.2.3
            setTimeout(() => {
                try {
                    let x = onRejected(self.reason);
                    resolvePromise(promise2, x, resolve, reject);
                } catch (e) {
                    reject(e);
                }
            });
        } else if (self.status === PENDING) {
            console.log('then裡面 PENDING')
            self.onFulfilled.push(() => {
                setTimeout(() => {
                    try {
                        let x = onFulfilled(self.value);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                });
            });
            self.onRejected.push(() => {
                setTimeout(() => {
                    try {
                        let x = onRejected(self.reason);
                        resolvePromise(promise2, x, resolve, reject);
                    } catch (e) {
                        reject(e);
                    }
                });
            });
        }
    });
    return promise2;
}

function resolvePromise(promise2, x, resolve, reject) {
    let self = this;
    //PromiseA+ 2.3.1
    if (promise2 === x) {
        reject(new TypeError('Chaining cycle'));
    }
    if (x && typeof x === 'object' || typeof x === 'function') {
        let used; //PromiseA+2.3.3.3.3 只能调用一次
        try {
            let then = x.then;
            if (typeof then === 'function') {
                //PromiseA+2.3.3
                then.call(x, (y) => {
                    //PromiseA+2.3.3.1
                    if (used) return;
                    used = true;
                    resolvePromise(promise2, y, resolve, reject);
                }, (r) => {
                    //PromiseA+2.3.3.2
                    if (used) return;
                    used = true;
                    reject(r);
                });

            }else{
                //PromiseA+2.3.3.4
                if (used) return;
                used = true;
                resolve(x);
            }
        } catch (e) {
            //PromiseA+ 2.3.3.2
            if (used) return;
            used = true;
            reject(e);
        }
    } else {
        //PromiseA+ 2.3.3.4
        resolve(x);
    }
}

let P1 = new Prom((a, b) => {
    setTimeout(() => {
        a('123')
    }, 1000)
})

P1.then((value) => {
    console.log(value)
    return '安安'  // 此then會返回已實現的Promise
})
.then((value) => {
    console.log(value)
})
```

以下為output

```bash
then裡面 PENDING    <----同步階段
then裡面 PENDING    <----同步階段
123   <----非同步階段
安安  <----非同步階段
```

此為註一的測試，目前想知道甚麼情況會跑到FULFILLED和REJECTED區塊

改成這樣的話

```bash
let P1 = new Prom((a, b) => {
    setTimeout(() => {
        a('123')
    }, 1000)
})

P1.then((value) => {
    console.log(value)
    let P2 = new Prom((a, b) => {
        setTimeout(() => {
            a('465')
        }, 1000)
    })
    return P2
})
.then((value) => {
    console.log(value)
})
```

第一個then改成回傳Promise

output:

```bash
then裡面 PENDING
then裡面 PENDING
123
then裡面 PENDING
465
```