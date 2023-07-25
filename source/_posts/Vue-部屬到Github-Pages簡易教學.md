---
title: Vue | 部屬到Github Pages簡易教學
date: 2022-08-18 14:53:10
tags:
  - Vue
  - Github Pages
categories: Vue
---
## 步驟

### 建立 repository

首先在Github新建一個repository

### 打包專案

1.  在 `vue.config.js & vite.config.js` 內新增base key
    
    ```jsx
    export default defineConfig({
      base: '/',
      plugins: [
        vue()
      ]
    })
    ```
    
    如果你的repo為`<USERNAME>.github.io`，那base為`'/'`
    
    如果你的repo為一般名稱，那base為`'/<repo-name>'`
    
2. 儲存後，打包專案
    
    ```jsx
    npm run build
    ```
    

### 把專案推到Github

1.  打包完後根目錄會出現`dist`資料夾，此時目錄到這個資料夾下，創立一個git repo，分支名稱為`gh-pages`，push到Github上。
    
    ```jsx
    git init
    
    git add .
    
    git commit -m "deploy"
    
    git branch -M gh-pages    // 這個指令可以創建並移動到該分支上
    
    git push -u 遠端url gh-pages
    ```
    
2. 在Github repo → Setting → Pages，選擇branch，資料夾不用動(預設root)，之後按Save

![Untitled](1.png)

1. 成功後，會出現以下這個，點開網址，就可以看到專案
    
    ![Untitled](2.png)
    

## 官方文件

https://vitejs.dev/guide/static-deploy.html