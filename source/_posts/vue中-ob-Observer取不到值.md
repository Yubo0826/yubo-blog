---
title: 'Vue中__ob__: Observer取不到值?'
date: 2022-09-08 15:06:07
tags:
  - Vue
  - BugFix
categories: Vue
---
## 情況

在mounted階段我要處理一個經由後端拿到的資料，此資料為一個array。

我發現我無法對這個array做遍歷，而後我console.log打印出來的值為

![Untitled](1.png)

## 淺談****Observer****

observer是vue對data所設置的監控器，常見的應用是，比如做一個非同步請求賦值給arr的動作，還沒拿到這個資料前，arr是undefined，但如果是console打印arr，則會出現上面的樣子，當下打印時arr是空的，但打印出來卻有值的情況，就是因為vue對arr設置observer。

## 解決

因為在mounted階段，我還沒拿到由後端發送過來的值，所以處理方式改為watch arr，當值發生改變我再去對arr做處理，拿到的資料就是正常的。

![Untitled](2.png)