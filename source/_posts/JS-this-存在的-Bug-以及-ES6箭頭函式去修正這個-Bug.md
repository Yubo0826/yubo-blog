---
title: JS | this 存在的 Bug 以及 ES6箭頭函式去修正這個 Bug
date: 2022-04-03 16:03:40
tags: JavaScript
categories: JavaScript
---
當在function內使用this，而這個function 定義位置在method內，例如:

```jsx
const obj = {
  func1() {
    console.log('func1 this', this); // "this" is obj
    (function func2() {
      // "this" binding is lost here
      console.log('func2 this', this); // "this" is Window
    })();
  }
};

obj.func1();
```

這個functoin內的this指向的會是全域物件window，而不是預期指向obj，這是JS本身就存在的BUG，

但在ES6箭頭函式的出現，有解決掉這個BUG，原因是箭頭函式定義位置的上一層regular function & method的 `this` 代表誰，箭頭函式內的 `this` 就代表誰。

```jsx
const obj = {
  func1() {
    console.log('func1 this', this); // "this" is obj
    (() => {
      console.log('func2 this', this); // "this" is obj
      // "this" was bound to func1's "this" reference
    })();
  }
};

obj.func1();
```

再看一個範例: 

在一般函式下

```jsx
const person = {
  firstName: 'Bob',
  getName() {
    console.log(this.firstName);
  }
};

person.getName();// Bob
```

使用箭頭函式

```jsx
const person = {
  firstName: 'Bob',
  getName: () => {
    console.log(this.firstName);
  }
};

person.getName(); // undefined
```
