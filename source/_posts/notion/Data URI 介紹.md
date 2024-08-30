---
created: 2023-08-31 16:48:00
tags:
  - Data URI
updated: 2024-08-30 10:41:00
title: Data URI 介紹
id: 4a5f5f38-f2a7-467f-b4e3-91a471205e07
---

Data URI 是一種檔案格式，資料全部都是經過 base64 編碼之後，以文字的方式來儲存的，不需要花費資源去載入檔案。

## 表達式定義

```text
data:[<mediatype>][;base64],<encoded data>
```

ex:

```html
<a href="data:text/plain;charset=utf-8,123456" download="test1">download</a>
```

根據[RFC 2397 § 2](https://www.rfc-editor.org/rfc/rfc2397#section-2)

若沒有[<mediatype>]，默認為`text/plain;charset=US-ASCII`

但沒有指定 charset 呢? 個人實測

![](https://prod-files-secure.s3.us-west-2.amazonaws.com/fab01c2d-ab6a-4db7-b508-4a953f2d8add/3e42e865-75c6-4a42-bbe1-0fd2162ee110/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45HZZMZUHI%2F20240830%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20240830T024712Z&X-Amz-Expires=3600&X-Amz-Signature=0e1c8d44c3af43ac94ab9e2619cb8679a0f2b59a1266eacaa49ebf34d47dfd62&X-Amz-SignedHeaders=host&x-id=GetObject)

![](https://yubo0826.github.io//source/imagesd25dc53a-9aa4-4ef6-8976-206cf7036fd9.png)

有中文還是要加`charset=utf-8`

## Mediatype

全名是 Internet media type(網際網路媒體類型)，也稱為 MIME type 或 Content type

### 格式

`type / subtype [ ; 可選參數]`

常用的已註冊的 type 包括`application`、`multipart`、`text`、`image`

sdbtype 通常是根據 type 細分的檔案類型、後面還會加上其他資訊，詳細的[維基](https://zh.wikipedia.org/zh-tw/%E4%BA%92%E8%81%94%E7%BD%91%E5%AA%92%E4%BD%93%E7%B1%BB%E5%9E%8B)有

### 常見範例

1. application/json
2. application/pdf
3. application/zip
4. audio/mp4
5. image/jpeg
6. image/svg+xml
7. text/csv
8. text/html
9. text/plain: 純文字內容

## 編碼

以下是舉例常見的編碼

1. base64 6 個 bits 組成，共可表示 64 個字，索引表詳見[維基](https://zh.wikipedia.org/zh-tw/Base64)

   ```html
   <a href="data:text/plain;base64,SGVsbG8sIFdvcmxkIQ%3D%3D" download
     >download</a
   >
   ```

   下載下來為 text 檔，內容為`Hello, World!`

2. ASCII 8 個 bits 組成(1bytes)，共可表示 127 個字

   ```html
   <a href="data:text/plain;charset=US-ASCII,123456" download="test1">download</a
   ```

   \*ASCII 是美國那邊的標準，所以主要表示英文及特殊符號

3. UTF 1-8 個 bytes 不等，常見英文 1byte，漢字通常 3bytes，例如`國`是 `E5 9D 8D`

   ```html
   data:text/plain;charset=utf-8
   ```

補充:

- 不同語言文本打開會出現編碼的原因: 用 A 方式把文把編碼成二進制，然後用 B 方式解碼，就會產生亂碼

## Data URL 圖片應用

```html
<img src="data:image/png;base64,圖片base64碼" />
```

### 優點

直接寫在 html/css 裡面，減少 http 請求

### 缺點

1. base64 編碼的大小比原資源還大 4/3 左右，也就是 Data URL 生成的圖片比 Binary 還大
2. Data URL 圖片不會被瀏覽器存在 cache，所以每次訪問網站都要下載一遍
3. 開發端不利於維護，程式碼太過冗長，但有套件可以優化

### 線上圖片轉換 Data URL

[https://onlinetools.com/image/convert-image-to-data-uri](https://onlinetools.com/image/convert-image-to-data-uri)

## 補充資料

- [使用 DATA URI 將圖片以 Base64 編碼並內崁至網頁中，加速載入速度](https://blog.gtwang.org/web-development/minimizing-http-request-using-data-uri/)
- [base64、utf-8、二进制有什么关系？](https://zhuanlan.zhihu.com/p/476477271)
