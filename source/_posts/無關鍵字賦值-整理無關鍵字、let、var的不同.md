---
title: 無關鍵字賦值? | 整理無關鍵字、let、var的不同
date: 2022-03-30 16:01:17
tags: JavaScript
categories: JavaScript
---
var是全域變數宣告?

這句可能你覺得很對的一句話，實際上是個的錯誤...

無關鍵字賦值、var 宣告、let 宣告最大的差別在於**生存區域的不同**。無關鍵字賦值意味著全域變數的宣告，當然你在全域範圍使用 var/let 宣告也是全域的。

原文網址:

https://tw.alphacamp.co/blog/javascript-var-let-const

裏頭有相當多詳細觀念

整理:

1.  無關鍵字賦值: 意味著全域變數的宣告
    
    ```jsx
    function printG(){
      g = 1
      console.log(`printG: `, g)
    }
    
    printG() // => printG: 1
    console.log(`Global G:`, g) // => Global G: 1
    ```
    
2.  var宣告: 其生存範圍存在於函數內
    
    ```jsx
    function printV1(){
      var v = 2
      console.log(`printV1:`, v)
    }
    
    printV1() // => pritnV1: 2
    try{
      console.log(v)// error  
    } catch {
      console.log('not find v');
    }
    ```
    
3. let宣告: 變數是屬於區塊的(block)

## 提升(**Hoisting)**

> JavaScript 僅提升宣告的部分，而不是初始化。如果在使用該變數後才宣告和初始化，那麼該值將是 undefined。
> 

下面code我一開始看以為是輸出

9

2

但因為提升的關係，JS在執行printV0函式時，會優先將v1變數放入記憶體，但尚未初始化，所以第一個會log出undefined。

```jsx
var v1 = 9

function printV0(){
  console.log(`printV2:`, v1) // undefined
  var v1 = 2
  console.log(`printV2:`, v1) // 2
}

printV0()
```