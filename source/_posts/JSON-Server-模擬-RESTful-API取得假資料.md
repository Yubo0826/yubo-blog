---
title: JSON Server | 模擬 RESTful API取得假資料
date: 2022-01-17 16:10:23
tags: 
- Node.js
- 套件
categories: Node.js
---
當練習或開發時需要透過 API取得資料，而手邊正沒有符合的 API可以用時，就可以 JSON Sever 這個 JS 套件，幫我們建立一個 API Server，只需要建立一個JSON形式的資料。

### JSON Server 安裝步驟

https://github.com/typicode/json-server

1. Install JSON Server
    
    ```jsx
    npm install -g json-server
    ```
    
2. 創建 db.json 並輸入資料
    
    ```jsx
    {
      "posts": [
        { "id": 1, "title": "json-server", "author": "typicode" }
      ],
      "comments": [
        { "id": 1, "body": "some comment", "postId": 1 }
      ],
      "profile": { "name": "typicode" }
    }
    ```
    
3. Start JSON Server
    
    ```jsx
    json-server --watch db.json
    ```
    
4. 訪問 http://localhost:3000/posts/1 就可以得到資料
    
    ```jsx
    { "id": 1, "title": "json-server", "author": "typicode" }
    ```
    

### 搭配 axios 實作

1. 專案資料夾 npm init (使用 npm前要先安裝 node)，創建 package.json ，package.json是掌管專案資訊的重要檔案。
2. **npm install** axios
3. 創建一個 main.js，輸入以下代碼
    
    ```jsx
    import axios from "axios";
    
    function api() {
        axios.get('http://localhost:3000/posts').then(
            (res) => {
                process.stdout.write(JSON.stringify(res.data));
            }
        )
    }
    
    api();
    ```
    
4. 終端輸入 node main.js ，run 我們的JS檔案，就可以看到抓取到的資料了。

![擷取.PNG](1.png)

### 自訂自己的 npm command

1. 在 package.json中，在 scripts去定義自己的npm command，這裡命名為 json，去操作 json-server 開啟指令，並設定 port 為8888。 
    
    ```jsx
    "scripts": {
            "json": "json-server --watch db.json --port 8888"
        },
    ```
    
2.  設定好後，在 終端機上輸入 npm run json 就可以執行 json-server --watch db.json --port 8888 這項指令。

相關文連結:

1.  [https://medium.com/html-test/從零開始-使用npm套件-317beefdf182](https://medium.com/html-test/%E5%BE%9E%E9%9B%B6%E9%96%8B%E5%A7%8B-%E4%BD%BF%E7%94%A8npm%E5%A5%97%E4%BB%B6-317beefdf182)
2. [https://medium.com/@debbyji/用-json-server-模擬-restful-api-f07abda3927c](https://medium.com/@debbyji/%E7%94%A8-json-server-%E6%A8%A1%E6%93%AC-restful-api-f07abda3927c)
3. https://ithelp.ithome.com.tw/articles/10232465