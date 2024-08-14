---
title: JS download 文本中#後面的字都不見了
date: 2023-09-04 17:28:25
tags:
- JavaScript
- BugFix
- encoded
categories: JavaScript
---
## 發生情況

今天想讓使用者下載一個文本檔，

```jsx
var mytext = encodeURI( "111#1111" );
var a = document.body.appendChild(document.createElement("a"));
a.download = "My_output.html"; 
a.href = "data:text/html," + mytext; 
a.click();
```

但下載下來的內容是`1111`

#後面(包含#)都不見了

![Untitled](1.png)

## 解決

看來大原因是解碼問題，細部為甚麼後面會消失就不知道了

encodeURI 改成 encodeURIComponent

```jsx
var mytext = encodeURIComponent( "1111#1111" );
var a = document.body.appendChild(document.createElement("a"));
a.download = "My_output.html"; 
a.href = "data:text/html," + mytext; 
a.click();
```

參考資料:

https://stackoverflow.com/questions/31789651/hashtag-within-string-breaks-javascript