---
title: 了解DOM元素尺寸及位置
date: 2023-06-19 15:33:27
tags: 
  - JavaScript
  - DOM
categories: JavaScript
---
在JS取得元素寬度及高度，有三組鼠性可以用，分別是offset、client、scroll

## 取得元素尺寸

### offsetWidth、offsetHeight

`offsetWidth`/`offsetHeight` 是「元素本身」的寬度/高度，並完整了包含了邊界、捲軸及padding。

![Untitled](1.png)

### clientWidth、clientHeight

`clientWidth`, `clientHeight` 是元素所包含的「子元素」的寬度及高度，其中包含了padding，但不包含邊界及捲軸。

![Untitled](2.png)

### scrollWidth、scrollHeight

`scrollWidth`/`scrollHeight` 也是元素所包含的「子元素」的「完整」寬度和高度，其中包含了超出捲軸之外的部分的寬度與高度。在沒有捲軸的情況下，這個值就等於 `clientWidth`/`clientHeight`。

## 取得元素相對位置

與尺寸大小相同，也有三組屬性可以用，分別是offset、client、scroll。

### offsetLeft、offsetTop

相對於父元素的水平/垂直距離。

### clientLeft、clientTop

等同於元素的border寬度

有scroll的情況下，包含scroll寬度。

### scrollLeft、scrollTop

內容頂端和捲軸頂端的相對距離。

## 取得元素絕對位置

要取得絕對位置(相對於文檔左上角)，可以使用offsetLeft和offsetTop與父元素迭代相加，直到到達根元素為止。

```jsx
function getAbsolutePosition(element) {
  var left = element.offsetLeft;
  var top = element.offsetTop;

  while (element.offsetParent) {
    element = element.offsetParent;
    left += element.offsetLeft;
    top += element.offsetTop;
  }

  return { left: left, top: top };
}

// 例子使用：
var element = document.getElementById("myElement");
var position = getAbsolutePosition(element);

console.log("元素的绝对左坐标：" + position.left + "px");
console.log("元素的绝对上坐标：" + position.top + "px");
```

## 來源

https://shubo.io/element-size-scrolling/

## 補充

offset請聯想到父元素