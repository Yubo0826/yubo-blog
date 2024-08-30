---
created: 2022-11-14 18:11:00
updated: 2024-08-30 10:43:00
title: Javascript 物件傳址
id: 95a7e83e-17bf-4939-a09e-9b84a129368d
---

## 物件傳址 Call by reference

```javascript
let obj1 = { a: 1 };
let obj2 = { a: 1 };

console.log(obj1 === obj2); // false
```

為啥`obj1 === obj2`會等於 false 呢?

因為在 JS 中，每個物件都是獨立存在的個體，在比較物件型別時，比較的是記憶體位置，亦即參考(reference)，而非值(value)。

## 物件內容比較

用`JSON.stringify()`

```javascript
let a = { x: 5, y: 6 };
let b = { x: 5, y: 6 };

console.log(a === b); // false
console.log(JSON.stringify(a) === JSON.stringify(b)); // true
```

## 物件拷貝

主要分為淺拷貝、深拷貝

### 淺拷貝

1. 把物件內容複製到一個空物件裡面

   ```javascript
   let obj1 = { a: 1 };
   let obj2 = Object.assign({}, obj);

   console.log(obj2); // { a: 1 }
   ```

1. 使用`...`物件展開運算子

   ```javascript
   let obj1 = { a: 1 };
   let obj2 = { ...obj1 };

   console.log(obj2); // { a: 1 }
   ```

### 淺拷貝缺點

```javascript
let obj1 = {
  a: 10,
  b: {
    foo: 20,
  },
};

let obj2 = { ...obj1 };

obj1 === obj2; // console.log: false
obj1.b === obj2.b; // console.log: true
```

可以注意到內層的物件還是指向相同記憶體位置。所以淺拷貝只有改變外層容器的地址，沒動到內層。

### 深拷貝

解決淺拷貝的缺點，完全複製一份

```javascript
let obj1 = {
  a: 10,
  b: {
    foo: 20,
  },
};

let obj2 = JSON.parse(JSON.stringify(obj1));

obj1 === obj2; // console.log: false
obj1.b === obj2.b; // console.log: false
```

先用`JSON.stringify`把 obj1 轉成 JSON string，再用`JSON.parse`轉成物件，就可以完成複製

### 深拷貝缺點

`JSON.stringify`會自動過濾掉 key 為  `symbol`  或值為  `undefined`  的屬性

詳細的解決可參考:

[https://blog.techbridge.cc/2018/06/23/javascript-call-by-value-or-reference/](https://ithelp.ithome.com.tw/articles/10223178)

參考文章

1. [https://hackmd.io/@chupai/B13YRDJJB#JavaScript-的「傳值」與「傳址」](https://hackmd.io/@chupai/B13YRDJJB#JavaScript-%E7%9A%84%E3%80%8C%E5%82%B3%E5%80%BC%E3%80%8D%E8%88%87%E3%80%8C%E5%82%B3%E5%9D%80%E3%80%8D)
2. [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)
3. [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
