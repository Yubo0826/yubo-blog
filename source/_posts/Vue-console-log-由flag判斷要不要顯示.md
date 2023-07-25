---
title: Vue console.log 由flag判斷要不要顯示
date: 2023-07-11 13:43:02
tags: 
- Vue
- JavaScript
categories: Vue
---
### 需求

登入後，取的該帳號要不要顯示`console.log`的flag

### 實作

`main.js` 註冊一個全域物件`$console`

```jsx
Vue.prototype.$console = {
  value: false,
  set(value) {
    this.value = value
    const log = console.log
    console.log = function(msg) {
      if (this.value) {
        log(msg)
      }
    }
  }
}
```

在登入後的回調函式中，修改`$console.value`，以及在`console.log`加上一個判斷

```jsx
this.$console.set(response.data.showLog) // true or false
```