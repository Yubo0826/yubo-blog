---
title: JS | 短路求值(Short-circuit evaluation)
date: 2023-03-27 10:57:33
tags: JavaScript
categories: JavaScript
---
# JS | 短路求值(Short-circuit evaluation)

### 情境

跟後端做串接的時候，前端拿到value有可能不符預期拿到null or undefined，如果要用這value call一些指定型別才有的method，那就可能會TypeError

### 舉例

"Cannot read properties of undefined (reading 'toLowerCase')"發生在一個undefined value call **`toLowerCase()`**method所產生的TypeError

![Untitled](Untitled.png)

為了解決這個錯誤，需要加判斷式，但其實**邏輯運算子OR**就可以辦到了， 用`||`設定預設值，這樣程式碼看起來整潔多了，而這樣的寫法叫短路求值

```jsx
const moreVar = undefined;

const str = moreVar || ''

const result = str.toLowerCase();

console.log(result); // output ""
```

當value是null也適用

```jsx
const moreVar = null;

const str = moreVar || ''

const result = str.toLowerCase();

console.log(result); // output ""
```

### 補充一

在JavaScript中，`0`、`""`、`false`、`null`、`undefined`和`NaN`都會是`false`

### 補充二: **Optional chaining operator**

ES11(2020)新增的語法，符號為`?.`，使用在物件property(key value)和call function，當呼叫物件的key或是function為`undefined` or `null`，可以短路求`undefined`而非拋出一個錯誤

舉例:

```jsx
const adventurer = {
  name: 'Alice',
  cat: {
    name: 'Dinah'
  }
};

const dogName = adventurer.dog?.name;
console.log(dogName);
// Expected output: undefined

console.log(adventurer.someNonExistentMethod?.());
// Expected output: undefined
```

### 參考資料

1. [https://bobbyhadz.com/blog/javascript-cannot-read-property-tolowercase-of-undefined#cannot-read-properties-of-undefined-reading-tolowercase](https://bobbyhadz.com/blog/javascript-cannot-read-property-tolowercase-of-undefined#cannot-read-properties-of-undefined-reading-tolowercase)
2. [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)

