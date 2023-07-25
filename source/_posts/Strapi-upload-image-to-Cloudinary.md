---
title: Strapi upload image to Cloudinary
date: 2022-07-01 14:46:51
tags:
  - Strapi
  - Cloudinary
categories: Strapi
---
## 關於Cloudinary

[https://cloudinary.com/](https://cloudinary.com/console/c-3dc4299f4f35933e5add0dfe1b5ce3/getting-started)

![Untitled](cloud.png)

Cloudinary 是一個為網頁和行動裝置開發者提供的媒體管理平台，簡單來說就是一個基於雲端圖片處理、管理、儲存、美化於一體的平台，同时也具備影像抓取功能，提供API接口。

## 為甚麼要上傳到Cloudinary

當strapi部屬到heroku，由於heroku免費專案是如果30分鐘沒有api call，伺服器就會進入休眠，每當進入休眠，strapi local media內的圖片就會不見，所以需要另外存雲端平台上。

## 步驟

1.  先創立一個strapi專案，並部屬到heroku
https://docs.strapi.io/developer-docs/latest/setup-deployment-guides/deployment/hosting-guides/heroku.html#_2-login-to-heroku-from-your-cli
2. 在Cloudinary新增一個資料庫，在dashboard內可以看到Account Details等等需要用到
3. 到strapi專案資料夾，終端機輸入指令
    
    ```html
    npm install @strapi/provider-upload-cloudinary --save
    ```
    
4. ./config/plugins.js路徑下加入以下code
*注意: plugins.js 檔名有s
    
    ```jsx
    module.exports = ({ env }) => ({
          // ...
          upload: {
            config: {
              provider: 'cloudinary',
              providerOptions: {
                cloud_name: env('CLOUDINARY_NAME'),
                api_key: env('CLOUDINARY_KEY'),
                api_secret: env('CLOUDINARY_SECRET'),
              },
              actionOptions: {
                upload: {},
                delete: {},
              },
            },
          },
          // ...
        });
    ```
    
5. 然後在根目錄創建or修改`.env`，加入以下3行，資訊都在Cloudinary Dashboard內
    
    ```jsx
    CLOUDINARY_NAME = cloudinary-name
    CLOUDINARY_KEY = cloudinary-key
    CLOUDINARY_SECRET = cloudinary-secret
    ```
    
    另外也要在heroku Settings → Config Vars 一樣要設置那三個變數
    
6. 在./config/middlewares.js內，在module.exports 陣列內，增加以下一個元素
    
    ```jsx
    {
      name: 'strapi::security',
      config: {
        contentSecurityPolicy: {
          useDefaults: true,
          directives: {
            'connect-src': ["'self'", 'https:'],
            'img-src': ["'self'", 'data:', 'blob:', 'res.cloudinary.com'],
            'media-src': ["'self'", 'data:', 'blob:', 'res.cloudinary.com'],
            upgradeInsecureRequests: null,
          },
        },
      },
    },
    ```
    
    完成後應該會長這樣
    
    ```jsx
    module.exports = [
      'strapi::errors',
      'strapi::security',
      'strapi::cors',
      'strapi::poweredBy',
      'strapi::logger',
      'strapi::query',
      'strapi::body',
      'strapi::session',
      'strapi::favicon',
      'strapi::public',
      {
        name: 'strapi::security',
        config: {
          contentSecurityPolicy: {
            useDefaults: true,
            directives: {
              'connect-src': ["'self'", 'https:'],
              'img-src': ["'self'", 'data:', 'blob:', 'dl.airtable.com', 'res.cloudinary.com'],
              'media-src': ["'self'", 'data:', 'blob:', 'dl.airtable.com', 'res.cloudinary.com'],
              upgradeInsecureRequests: null,
            },
          },
        },
      },
    ];
    ```
    
7. 完成後git add . → git commit 後，如果是用heroku cl 就輸入指令
    
    ```jsx
    git push heroku HEAD:main
    ```
    
    如果是用heroku+github，就在heroku 手動deploy選好分支，然後按下deploy就好。
    

## 參考資料

1.  https://strapi.io/blog/add-cloudinary-support-to-your-strapi-application
2. https://docs.strapi.io/developer-docs/latest/setup-deployment-guides/deployment/hosting-guides/heroku.html#_2-retrieve-database-credentials