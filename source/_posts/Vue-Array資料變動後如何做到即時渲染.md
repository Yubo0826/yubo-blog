---
title: Vue Array資料變動後如何做到即時渲染 ?
date: 2022-04-18 14:59:41
tags: Vue
categories: Vue
---
## 前言

在Vue中，響應式(reactivity)是Vue的一大特色，但由於 JavaScript 的限制，有些情況Vue不能檢測array或object的變化來及時渲染頁面，這篇主要針對array來迴避這個問題，以及如何讓他即時渲染。

## 不能監測的情況

Vue不能監測以下數據變化:

1.  直接賦值
    
    ```jsx
    vm.items[indexOfItem] = newValue
    ```
    
2. 修改數組長度
    
    ```jsx
    vm.items.length = newLength
    ```
    

## 如何即時渲染

舉例:

```jsx
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x'     // 不是Reactivity
vm.items.length = 2   // 不是Reactivity
```

以下兩種方法都可以實現和`vm.items[indexOfItem] = newValue`同樣效果，

同時也會在Reactivity System觸發狀態更新。

```jsx
vm.$set(vm.items, indexOfItem, newValue)
```

```jsx
vm.items.splice(newLength)
```