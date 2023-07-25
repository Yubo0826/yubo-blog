---
title: 使用v-on keypress遇到的問題
date: 2022-05-06 15:49:05
tags:
  - Vue
  - JavaScript
  - BugFix
categories: JavaScript
---
## 前言

使用vue，做輸入框過濾字的功能時，規定使用者只能打中文數字英文，不能打標點符號和空白字元。然而，不曉得甚麼原因抓不到文本框最新的value

```html
<textarea 
  v-model="Msg" 
  name="text" 
  id="comment" 
  placeholder="Please Enter Characters." 
  maxlength="100"
  @keypress="Msg.replace(/[^0-9a-zA-Z\u4E00-\u9FA5]/,''), 200);"
  >
  </textarea>
```

因為規定能打中文但不能打標點符號，但是打中文時都會按到標點符號按鍵，就會觸發到事件函數，要解決這個問題，就必須用keypress事件，如果是輸入中文字(注音)的話，使用 keypress 事件都不會被觸發。

## 原因

js的鍵盤事件有三種，keydown、keypress、keyup，keydown是按下鍵盤觸發，keypress跟keydown類似，差別在於keypress只針對可以輸出文字符號的按鍵有效，按ESC、backspace、方向鍵等就無效，最後keyup是鍵盤放開的那霎那觸發。

**而只有keyup事件是可以拿到最新的value。**

所以使用keypress或keydown時，就需要用setTimeout函數，讓事件函數執行的晚一點，就可以了。

```html
<textarea 
  v-model="Msg" 
  name="text" 
  id="comment" 
  placeholder="Please Enter Characters." 
  maxlength="100"
  @keypress="setTimeout(()=>{Msg.replace(/[^0-9a-zA-Z\u4E00-\u9FA5]/,'')}, 200);"
  >
  </textarea>
```

---