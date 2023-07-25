---
title: >-
  Module build failed: Error: ENOENT: no such file or directory, scandir… |
  Linux環境打包Vue Bug解決
date: 2023-01-10 15:11:22
tags:
 - Vue
 - Linux
 - BugFix
 - Build
categories: Linux
---
## 錯誤

部屬完後打開網站一片空白，log顯示錯誤有時503，有時404，有時顯示一個Module build faild

![Untitled](1.png)

![Untitled](2.png)

## 發生情境&操作流程

前端專案資料夾在linux環境，`npm i`安裝所有套件，再`npm build`打包，之後到dist目錄下把檔案放在tomcat的webapps下。

## 細看錯誤訊息

一開始在打包時並沒注意，但`npm run build`所顯示的錯誤提示有說”Miss binding your environment”

![Untitled](3.png)

聯想到跟瀏覽器log的錯誤有關

```jsx
Module build failed: Error: ENOENT: no such file or directory, scandir '/home/david.chou/rmc-remocloud-vue-dev/node_modules/node-sass/vendor'
```

他說找不到`node_modules/node-sass/vendor`目錄，

我一去檢查，還真的在node-sass目錄下找不到vendor資料夾，

此時，我又跑去window下的專案下看，發現是有的

![Untitled](4.png)

裡面有個資料夾，再裡面有個名叫binding.node的檔案

我想就是缺少這個導致npm run build有誤

![Untitled](5.png)

![Untitled](6.png)

黃框中他有提示說執行`npm rebuild node-sass`

我照樣執行後發現並沒有裝成功

報錯訊息:

![Untitled](7.png)

黃框中說明沒有權限產生一些檔案

## 解決

好這時找到原因了，原來是資料夾權限不夠，所以在根目錄輸入以下指令，以更改node_modules權限:

```bash
sudo chmod -R 777 node_modules
```

之後再輸入

`npm rebuild node-sass`

`npm run build`

然後把檔案部屬到server資料夾就解決了。