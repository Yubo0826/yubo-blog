---
title: Node.js | Buffer與Stream
date: 2023-03-07 15:26:25
tags: Node.js
categories: Node.js
---
## Buffer

Buffer(緩衝)是在資料移動時，提供資料暫存的記憶體位置，可以暫時的累積資料再繼續傳遞，就像在網路上看影片時，會有灰色區塊的緩衝暫存還沒播放到的影片資料。

在NodeJS中的緩衝，是以二進制資料(binary data)的形式暫存資料，可以透過函式如：`from`或`toString`進行資料的解碼或編碼，支援的編碼包含常用的`utf8`與`ASCII`等。

Buffer 在全域下不需要使用 require 就能使用。

以`new Buffer.from(字串,編碼)`可以直接建立新的Buffer，在定義新的Buffer變數時，編碼預設為`utf8`。

```jsx
let buffer = new Buffer.from("Hello");		//編碼預設為utf8
```

![Untitled](1.png)

## Stream

Stream(串流)指的是資料傳遞資料的序列流。

![Untitled](2.png)

在NodeJS中的Stream，作為EventEmitter的子類別，繼承了`on()`、`emit()`等方法，而串流根據資料流向的方向不同又分成`writable`(寫入)、`readable`(讀取)、`duplex`(讀寫)、`transform`(轉換)等類型，每一個串流的類別都繼承串流的方法。從官方的文件也可以發現串流的類別是一個抽象/基底類別(abstract/base class)，不會被直接使用，而是透過繼承取用的其屬性及方法，因此必須透過建立新的客製化物件再去繼承基底類別。

![Untitled](3.png)

### 實作

**讀取資料，印出資料內容**

在NodeJS中的`fs`模組提供了建立客製化Stream的功能，在讀取資料時，會先以Buffer的形式暫存。

使用`on()`註冊`data`事件，以監聽資料讀取的過程，而綁定該事件內容的函式，加入`chunk`作為讀取資料時的參數，`chunk`是串流中傳遞的資料片段，透過印出`chunk`可以確認資料讀取的情況。

```jsx
let fs = require("fs");

let readable = fs.createReadStream(__dirname + "/stream.txt");

readable.on("data", function(chunk){
  console.log(chunk);
});

let utf_readable = fs.createReadStream(__dirname + "/stream.txt", {encoding: "utf8"});
utf_readable.on("data", function(chunk){
  console.log(chunk);
});
```

![Untitled](4.png)

## 總結

Buffer是暫存資料區塊，Stream像是資料傳輸的通道