---
title: XML 是什麼?
date: 2023-04-15 11:05:13
tags: XML
---
## 簡介

可擴展標記語言 (XML) 是一種標記語言，可提供規則來定義任何資料。

可以使用自定義標籤定義資料，如: 

```html
<book>

	<title> Learning Amazon Web Services </title>

	<author> Mark Wilkins </author>

</book>
```

## 主要用途

資料與資料的串聯需要透過API的串接，不過API只是橋梁，中間的資料傳輸就是利用XML裝箱運送過橋的。

您可以使用 XML 在兩個以不同格式儲存相同資料的系統之間傳輸資料。例如，您的網站以 MM/DD/YYYY 格式儲存日期，但會計系統以 DD/MM/YYYY 格式儲存日期。您可以透過使用 XML 將資料從網站傳輸到會計系統。您的開發人員可以撰寫程式碼，自動進行下列轉換：

- 網站資料轉換為 XML 格式
- XML 資料轉換為會計系統資料
- 會計系統資料轉換為 XML 格式
- XML 資料轉換回網站資料

## 其他用途

1. Web 應用程式(可跟HTML搭配)
2. 文件
3. 資料類型

## XML與HTML

HTML 的目的是呈現和顯示資料。但是，XML 儲存和傳輸資料。

HTML 具有預先定義的標籤，但使用者可以在 XML 中建立和定義自己的標籤。

XML 標籤區分大小寫，但 HTML 不區分大小寫。

## 補充

1. API 簡介:
Application Programming Interface 應用程式介面，AB兩端的資料拋接，一端提供資料輸入、另一端依據資料回傳結果，其目的在於「不必了解對方的技術與邏輯即可加速開發的共識」，如購物網站串接第三方刷卡金流API，讓購物網站開發人員無需了解金流技術，透過API串接方式即可使用該項技術。
2. 參考文件
https://aws.amazon.com/tw/what-is/xml/
https://www.tsg.com.tw/blog-detail112-xml.htm