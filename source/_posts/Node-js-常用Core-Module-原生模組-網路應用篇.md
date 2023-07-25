---
title: Node.js | 常用Core Module(原生模組) - 網路應用篇
date: 2023-02-18 15:24:23
tags: Node.js
categories: Node.js
---
## net

TCP 是 HTTP 通訊的基礎協定，作為 Server 與 Client 的封包傳送，用於一些底層上的資料確認通信使用。這不是網頁媒體使用，而只是一種資訊封包傳送技術。使用方式為透過 *[net](http://nodejs.cn/api/net.html#net_net)* 模組來進行架設 TCP 伺服器。我們需要設計兩個應用程式分別為 server.js 與 client.js，TCP 伺服器保持監聽模式等待 Client 進行連線進一步取得 Client 資料，並回應資料還給 Client。

```jsx
// server.js

const net = require('net');
const server = net.createServer(function (myConnect) {

  //顯示連線資訊
  console.log(`正在與 ${myConnect.remoteAddress}:${myConnect.remotePort} 建立連線`);

  //接收 Client Data 事件之處理
  myConnect.on('data', function (data) {
    console.log(`來自 ${myConnect.remoteAddress} 的 client 資料為 '${data}'`);

    //寫入資料至 Client 端
    const serverData=`hi! Client`;
    myConnect.write(serverData);
    console.log(`發送給 Client:${serverData}`)
  });

  //監聽 Client Close 事件之處理
  myConnect.on('close', function (had_error) {
    if (had_error) console.log('連線錯誤');
    else console.log('Client 連線已關閉，伺服器持續運作中。..');
  });
});

//啟用 Server 服務
const host = '192.168.1.105';
const port = 3999;
server.listen(port, host, function () {
  console.log(`伺服器服務中。...${host}:${port}`);
});
```

```jsx
// client.js

const net = require('net');
const client = new net.Socket(); //建立 Socket

//連線至 Server
const host = '192.168.1.105';
const port = 3999;
client.connect(port, host, function () {
  console.log(`連線至 Server ${host}:${port}`);

  //提供 client 資料傳送
  const clientData='hi! Server';
  client.write(clientData);
  console.log(`發送給 Server：${clientData}`)
});

//監聽 Server Data 事件之處理
client.on('data', function (data) {
  console.log(`Server 回應：${data}`);

  //請求關閉 Server 之連線
  console.log(`即將關閉伺服器連線。..`);
  client.destroy();
});

//接收 Server Close 事件之處理
client.on('close', function (had_error) {
  if (had_error) console.log('連線錯誤');
  else console.log('Server 連線已關閉');
});
```

**補:**

Socket https://stevenke.gitbooks.io/internetnote/content/chapter1.html

## http

HTTP 架構於前者 TCP 之上層協定，能作為 Web Server 與瀏覽器之間的資料傳送。我們能使用 *[http](http://nodejs.cn/api/http.html)* 模組來建立 Web Server 的相關類別、方法、事件，用於處理瀏覽器所發出的 HTTP 請求。

每當有 HTTP 請求到達服務器時，createServer 中傳入的函數就被自動執行。

```jsx
const http = require('http');

const server = http.createServer((request, response) => {
  // magic happens here!
});
```

實際上，由 createServer 構造函數返回的 Server 對象是一個 EventEmitter，我們在這里僅是對創建 server 和對它添加監聽事件進行了簡化處理。

```jsx
const server = http.createServer();
server.on('request', (request, response) => {
  // the same kind of magic happens here!
});
```

### 處理request

當處理一個請求時，第一件事你需要做的是看一下這個method和其訪問地址

```jsx
const { method, url } = request;
```

查看header ，所有的header都是小寫

```jsx
const { headers } = request;
const userAgent = headers['user-agent'];
```

傳入請求對象的 request 其實是 `ReadableStream` 對象， 這個Stream可以被監聽。我們可以通過監聽 'data' 和 'end' 事件從而把數據給取出來。

每次接收資料會觸發data事件，會得到一個Buffer，理想的做法是把Buffer加入一個Array中，等到傳輸結束後觸發end事件，再把Array拼接並轉成string。

```jsx
let body = [];
request.on('data', (chunk) => {
  body.push(chunk);
}).on('end', () => {
  body = Buffer.concat(body).toString();
  // at this point, `body` has the entire request body stored in it as a string
});
```

### 傳輸過程有錯誤發生的話

因為 request 是一個 `ReadableStream` 對象，它同樣也是 `EventEmitter` 對象。所以當有錯誤發生時，表現的行為是很相像的。當有錯誤在 request 流上發生時，它會自動激發自身的 'error' 事件。如果你不去處理監聽這個事件，此錯誤將被拋出，這導致你的程序崩潰。 你應該無論如何都要添加 'error' 事件去監聽你的請求對象，哪怕你只是做一個日志或者用你自己的獨有方式去處理（當然，最佳的處理方式是返回一些出錯的信息，這已是後話了）。

```jsx
request.on('error', (err) => {
  // This prints the error message and stack trace to `stderr`.
  console.error(err.stack);
});
```

直到現在，已經談到了如何創建一個對象，如果從請求中獲取方法，request地址，request header和request body。當把它們組合到一起，它就看上去是這個樣子：

```jsx
const http = require('http');

http.createServer((request, response) => {
  const { headers, method, url } = request;
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    // At this point, we have the headers, method, url and body, and can now
    // do whatever we need to in order to respond to this request.
  });
}).listen(8080); // Activates this server, listening on port 8080.
```

再來會說到`response` 對象。它是一個 `ServerResponse`實例，而 ServerRespose 又是 `WritableStream`。它包含了很多方法可以用以把數據返回給客戶端。

### response

常用方法

| 方法 | 說明 |
| --- | --- |
| Write | 輸出String到客戶端 |
| WriteFile | 讀入資料檔 |
| Redirect | 轉換網頁 |
| Flush | 當BufferOutput 為True，輸出緩衝區所有內容，若沒設定，則是等所有任務都執行完之後才將所有Response.Write一起輸出 |
| End | 將目前暫存區內容輸出至瀏覽器後，停止將buffer中的資料傳給瀏覽器 |
| ClearContent | 清除Buffer中的內容 |

```jsx
response.writeHead(200, {
  'Content-Type': 'application/json',
  'X-Powered-By': 'bacon'
});
```

```jsx
response.write('<html>');
response.write('<body>');
response.write('<h1>Hello, World!</h1>');
response.write('</body>');
response.write('</html>');
response.end();
```

可以寫成

```jsx
response.end('<html><body><h1>Hello, World!</h1></body></html>');
```

全部整合到一起

```jsx
const http = require('http');

http.createServer((request, response) => {
  const { headers, method, url } = request;
  let body = [];
  request.on('error', (err) => {
    console.error(err);
  }).on('data', (chunk) => {
    body.push(chunk);
  }).on('end', () => {
    body = Buffer.concat(body).toString();
    // BEGINNING OF NEW STUFF

    response.on('error', (err) => {
      console.error(err);
    });

    response.statusCode = 200;
    response.setHeader('Content-Type', 'application/json');
    // Note: the 2 lines above could be replaced with this next one:
    // response.writeHead(200, {'Content-Type': 'application/json'})

    const responseBody = { headers, method, url, body };

    response.write(JSON.stringify(responseBody));
    response.end();
    // Note: the 2 lines above could be replaced with this next one:
    // response.end(JSON.stringify(responseBody))

    // END OF NEW STUFF
  });
}).listen(8080);
```

### 傳輸過程有錯誤發生的話

response 返回流同樣也會觸發 'error' 事件，某種程度上說你不得不自己去處理它。之前全部關於 request 消息流出錯的處理方法在這里也同樣適用。

### 服務器響應的示例代碼

針對以下條件回應:

1. 請求方法是POST方式
2. 訪問路徑是`/echo`

其他情況均回傳404

```jsx
const http = require('http');

http.createServer((request, response) => {
  if (request.method === 'POST' && request.url === '/echo') {
    let body = [];
    request.on('data', (chunk) => {
      body.push(chunk);
    }).on('end', () => {
      body = Buffer.concat(body).toString();
      response.end(body);
    });
  } else {
    response.statusCode = 404;
    response.end();
  }
}).listen(8080);
```

完整一點的話，加入stream的錯誤處理

```jsx
const http = require('http');

http.createServer((request, response) => {
  request.on('error', (err) => {
    console.error(err);
    response.statusCode = 400;
    response.end();
  });
  response.on('error', (err) => {
    console.error(err);
  });
  if (request.method === 'POST' && request.url === '/echo') {
    request.pipe(response);
  } else {
    response.statusCode = 404;
    response.end();
  }
}).listen(8080);
```

以上來源:

https://nodejs.org/zh-cn/docs/guides/anatomy-of-an-http-transaction/

### 其他範例

```jsx
const http = require('http');  //宣告原生模組 http

//建立伺服器且提供網頁狀態、HEAD 資訊、網頁內容
const server = http.createServer((request, response) => {

  // response.statusCode = 200;
  // response.setHeader('Content-Type', 'text/plain');  //設定 response HEAD
  // response.write('Hello World')  //設定網頁內容
  // response.end(); // response 送出
  // same as ↓

  response.writeHead(200,{'Content-Type':'text/plain'});　//response.setHeader() 適合循序參數調整且最後將由 writeHead() 進行嘗試合併，writeHeader() 內容優先為主
  response.end('Hello World');
});

//對伺服器進行開機並於完成作業後顯示文字至終端機
const hostname = '127.0.0.1', port = 3000; //設定 Web 主機與通訊埠
server.listen(port, hostname, () => {
  console.log(`
  Server running at http://${hostname}:${port}/
  Close Server press 'Ctrl+C' to exit
  `);
});
```

接著終端機輸入指令。透過提示的指令開啟瀏覽器至指定 URL 網頁得到提示與伺服器運作之真實網頁。

```bash
L:\nodeTest>node webServerTXT.js

  Server running at http://127.0.0.1:3000/
  Close Server Use Key 'Ctrl+C'
```

### 回應一個網頁格式的內容

```bash
require('http').createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/html' });
  res.write(`
    <!DOCTYPE html>
    <html lang="en">
    
    <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Test HTML</title>
    </head>
    
    <body>
      <h1>Hellor World</h1>
    </body>
    
    </html>
  `);
  res.end();
}).listen(3000, () => {
  console.log(' Server running at http://127.0.0.1:3000/');
});
```

> 原生 HTTP 模組並不是很彈性，大部分的人都會推薦使用 npm 元件 express 作為網頁伺服器服務首選。
> 

### 配合路由

```jsx
const http = require('http');
const fs = require('fs');

http.createServer((request, response) => {
  const pageDir = __dirname + '/public/';
  const result = function () { //進行路由轉換：將動態 URL 算出靜態檔案位置
    switch (request.url) {
      case '/apple':
        return {
          code: 200,
          filePath: pageDir + 'a.html'
        };
      case '/banana':
        return {
          code: 200,
          filePath: pageDir + 'b.html'
        };
      default:
        return {
          code: 404,
          filePath: pageDir + 'page404.html'
        };
    }
  }();　//直接執行獲得物件資料

  fs.readFile(result.filePath, function (err, content) {
    response.writeHead(result.code, { 'Content-Type': 'text/html' }); //設定 response HEAD
    if (!err) response.end(content);
    else response.end(pageDir + 'page404.html'); //如果檔案讀取失敗直接顯示 404 檔案
  });

}).listen(3333, "127.0.0.1", () => {
  console.log(`Server running!!
    try http://127.0.0.1:3333/apple show 'word A'
    try http://127.0.0.1:3333/banana show 'word B'
    try http://127.0.0.1:3333/cat show '404 ERROR'
    `);
});
```