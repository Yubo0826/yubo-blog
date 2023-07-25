---
title: Node.js | Local Modules（自建模組）
date: 2023-02-17 15:21:07
tags: Node.js
categories: Node.js
---
進行自建模組編寫時，最後需使用 export 方式傳出去。屆時任何應用進行 request 載入時才能正常接洽使用回傳內容。export 本身是一個變數，它屬於整個 module 模型下的原生練物件。因此只要將想回傳的東西指定給 export（可以是字串或文字）即可。

### 方法一

將 exports 當作物件，指定三種名稱函式

```jsx
// mod.js

exports.en = function (userName) {
  console.log('Hello! ' + userName);
};
exports.tw = function (userName) {
  console.log('你好！' + userName);
};
exports.hk = function (userName) {
  console.log('雷侯！' + userName);
};
```

### 方法二

將完整的物件指定給 export

```jsx
// mod.js

const hello = {
  en: function (userName) {
    console.log('Hello! ' + userName);
  },
  tw: function (userName) {
    console.log('你好！' + userName);
  },
  hk: function (userName) {
    console.log('雷侯！' + userName);
  }
};
module.exports = hello;  
/********************
exports = hello;       也能直接寫成這樣省略 module 字樣
********************/

/****另一種簡化後的寫法
module.exports = {
  en: function (userName) {
    console.log('Hello! ' + userName);
  },
  tw: function (userName) {
    console.log('你好！' + userName);
  },
  hk: function (userName) {
    console.log('雷侯！' + userName);
  }
};
****/
```

## 發request取得結果

```jsx
// test.js

/*
  自訂模組的 require時需指定相對路徑，而原生或 npm 包裝的模組不需要
  .js 副檔名可省略，將會自動去尋找屬於 js 副檔名之檔名

  const myMod = require('./mod.js');
*/
const myMod = require('./mod');
myMod.tw("Loki");
```

### 執行應用程式

```bash
L:\nodeDemo>node test.js
你好！Loki
```