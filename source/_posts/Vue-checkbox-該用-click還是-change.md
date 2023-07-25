---
title: Vue checkbox 該用@click還是@change?
date: 2022-10-13 15:03:29
tags:
  - Vue
  - BugFix
categories: Vue
---
## 問題

```html
<input
  v-model="checkboxModelName"
  @click="displayColumn()"
  class="remoCloud-input"
	type="checkbox"
  >
```

`displayColumn()`是按了checkbox會觸發的方法，他會把`checkboxModelName`的值傳到後端，之後再存在資料庫。

但現在問題是似乎存進資料庫。

## 解決

應該把`@click`換成`@change`，因為`@click`代表按了馬上觸發方法，但這時`checkboxModelName`值還沒發生改變，換成`@change`代表checkbox value發生變化才會出發方法。