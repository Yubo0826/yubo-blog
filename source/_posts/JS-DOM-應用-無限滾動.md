---
title: JS DOM 應用- 無限滾動
date: 2023-05-29 15:29:57
tags: 
  - JavaScript
  - DOM
categories: JavaScript
---
## 當元素有自己的scroll

完整內容高度 (`scrollHeight`) = 內容頂端與捲軸頂端的距離 (`scrollTop`) + 捲軸本身高度 (`clientHeight`) + 內容底端與捲軸底端的距離。

先認識幾個property

`scrollHeight`

![Untitled](1.png)

`clientHeight`

![Untitled](2.png)

也就是

![Untitled](3.png)

`scrollTop`

滾動調離頂端的距離，如果沒有滾動條，那scrollTop就是0

https://developer.mozilla.org/zh-TW/docs/Web/API/Element/scrollTop

總結:

「內容底端距離捲軸底端的距離」＝ `elem.scrollHeight - elem.scrollTop - elem.clientHeight`

判斷距離=0 or < 自定義的距離，就加載。

https://shubo.io/element-size-scrolling/

## 當scroll在body

`window.innerHeight + window.pageYOffset >= document.body.offsetHeight`