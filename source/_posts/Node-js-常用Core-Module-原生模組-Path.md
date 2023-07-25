---
title: Node.js | 常用Core Module(原生模組) - Path
date: 2023-02-17 15:23:24
tags: Node.js
categories: Node.js
---
*[path](http://nodejs.cn/api/path.html)*主要是在進行 fs 檔案目錄之操作讀取寫入時，會遇到的字串轉換處理。

```bash
const path = require('path');

/* normalize 協助拔除 `.`, `..`, `\\`，以及修正如 windows 作業系統的路徑表示符號 */
console.log(path.normalize('C:////temp\\\\/\\/\\/foo/bar'));
//print C:\temp\foo\bar

/* join 能將指定字串進行合併 */
console.log(path.join(__dirname,'index.html'));
//print L:\nodeDemo\index.html

/* 
basename 取出字串之檔名
extname 取出字串之附檔名
dirname 取出字串之目錄路徑
*/

console.log(__filename);
console.log(path.basename(__filename));
console.log(path.extname(__filename));
console.log(path.dirname(__filename));
//print L:\nodeDemo\index.js
//print index.js
//print .js
//print L:\nodeDemo
```

補:

執行 Node 應用時，能透過 **`__filename`** 與 **`__dirname`** 全域變數來取得該應用程式所在之檔案名稱或目錄，對於需要找到相對路徑處理上有很大的幫助。

```jsx
console.log(`
  dirname 應用目錄 ${__dirname}
  filename 檔案路徑 ${__filename}
`);
```

```bash
L:\nodeDemo>node .

  dirname 應用目錄 L:\nodeDemo
  filename 檔案路徑 L:\nodeDemo\index.js
```