---
title: Strapi deploy to google app engine guild
date: 2023-02-23 13:49:15
tags:
- Strapi
- Google App Engine
- GCP
- Deploy to Cloud
categories: Strapi
---
## 安裝Google Cloud CLI

https://cloud.google.com/cli?hl=zh-tw

## 教學資源

### 官方文件教學

https://docs.strapi.io/developer-docs/latest/setup-deployment-guides/deployment/hosting-guides/google-app-engine.html

https://docs-v3.strapi.io/developer-docs/latest/setup-deployment-guides/deployment/hosting-guides/google-app-engine.html

### 其他教學

https://blog.devops.dev/deploy-strapi-v4-cms-in-google-cloud-run-using-github-google-cloud-build-and-integrate-google-8b61d1f5f4fa

### 影片教學

https://www.youtube.com/watch?v=4Z79lmOpHGo&ab_channel=GrandReality

## upload media config 設定

教學網址

https://github.com/strapi-community/strapi-provider-upload-google-cloud-storage/blob/master/README.md

1. 先在gcp的cloud storage建立一個bucket
[Create buckets  |  Cloud Storage  |  Google Cloud](https://cloud.google.com/storage/docs/creating-buckets)
2. 安裝套件
    
    ```bash
    yarn add strapi-provider-upload-google-cloud-storage
    ```
    
3. 建立`/config/plugins.js`
    
    ```bash
    module.exports = {
        upload: {
          config: {
            provider: 'strapi-provider-upload-google-cloud-storage',
            providerOptions: {
                bucketName: '<bucket-name>',
                publicFiles: false,
                uniform: false,
                basePath: '',
            },
          },
        },
    }
    ```
    
4. 更改`/config/middlewares.js` 
    
    ```bash
    module.exports = [
      'strapi::errors',
      {
        name: 'strapi::security',
        config: {
          contentSecurityPolicy: {
            useDefaults: true,
            directives: {
              'connect-src': ["'self'", 'https:'],
              'img-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
              'media-src': ["'self'", 'data:', 'blob:', 'storage.googleapis.com'],
              upgradeInsecureRequests: null,
            },
          },
        },
      },
      'strapi::cors',
      'strapi::poweredBy',
      'strapi::logger',
      'strapi::query',
      'strapi::body',
      'strapi::favicon',
      'strapi::public',
    ];
    ```
    
5. 重新deploy到app engine，就可以上傳圖片!

## 修改Cloud Storage權限

1. 到 GCP控制台，依照下圖更改設定
    
    ![Untitled](1.png)
    
2. 下方新增授予存取權
    
    ![Untitled](2.png)
    

## 重新佈署

點開

![Untitled](3.png)

到專案目錄下

輸入

```bash
gcloud app deploy
```

## 遇到的錯誤

1. 
    
    https://stackoverflow.com/questions/73870911/cannot-connect-to-postgres-database-from-strapi-on-google-app-engine
    
    遇到這個bug，解決方法是啟用****sqladmin API****
    
2. 
在dashboard→media library下載圖片
    
    ![Untitled](4.png)
    
    目前無解，但影響不大