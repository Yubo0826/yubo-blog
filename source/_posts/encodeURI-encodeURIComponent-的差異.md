---
title: encodeURI encodeURIComponent 的差異
date: 2023-09-04 17:23:42
tags: 
- encoded
- JavaScript
- Web
categories: JavaScript
---
## URL組成架構

URL只能由合法的字符組成，合法字符分成兩大類

- URL 元字符: `;`、`,`、`/`、`?`、`:`、`@`、`$`、`=`、`+`、`#`
- 語意字符: `a-z`、`A-Z`、`0-9`、`-`、`_`、`.`、`!`、`~`、`*`、單引號`’`、`()`

除了上述字符，其餘字符出現在URL中皆需轉譯，規則根據操作系統的默認編碼，每個字符轉為`% + 兩個大寫16進制字母`

例如

`https://www.ptt.cc/bbs/Baseball/search?q=統一`

因為漢字統一不是合法字符，所以統一被瀏覽器轉成UTF-8字元

`https://www.ptt.cc/bbs/Baseball/search?q=%E7%B5%B1%E4%B8%80`

## 編碼/解碼 方法

- **encodeURI()**
- **encodeURIComponent()**
- **decodeURI()**
- **decodeURIComponent()**

## encodeURL

適用於**整個URL**，參數是一個URL(string)，他會將元字符和語意字符之外的字符進行轉譯(UTF-8編碼)

```jsx
let url = 'https://www.ptt.cc/bbs/Baseball/search?q=統一'
let encodeURL = encodeURL(url)
```

## **encodeURIComponent**

適用於URL位址後的參數，會轉譯(UTF-8編碼)除了語意字符以外的所有字符，即元字符也會轉譯，所以他不能轉譯整個URL，不然`/:`等會被轉譯掉

```jsx
let comp = encodeURIComponent("統一")
let encodeURL = encodeURL('https://www.ptt.cc/bbs/Baseball/search?q='') + comp
```

## 使用時機

- encodeURI適用於沒有參數的URL
例如: https://www.facebook.com/
- encodeURIComponent就適用於有參數的URL，我想大多時候用encodeURIComponent的機會會比較多