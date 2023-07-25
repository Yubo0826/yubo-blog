---
title: Typescript 任意屬性
date: 2023-06-07 13:39:08
tags: TypeScript
categories: TypeScript
---
## ****Index Signatures 任意屬性****

有時候知道value的狀態，但還不確定屬性名，就可以使用任意屬性來描述可能的型別。

```jsx
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```

此範例定義了任意屬性propName的屬性名字型別是string，然後value是任意型別。

如果是[propName: number]就是只能取number型別的屬性名。

這裡的propName可以隨意取，取[key: string]也可以。

## 範例

```jsx
interface Person {
    name: string;
    age?: number;
    [propName: string]: string;
}

let tom: Person = {
    name: 'Tom',
    age: 25,
    gender: 'male'
};

// index.ts(3,5): error TS2411: Property 'age' of type 'number' is not assignable to string index type 'string'.
// index.ts(7,5): error TS2322: Type '{ [x: string]: string | number; name: string; age: number; gender: string; }' is not assignable to type 'Person'.
//   Index signatures are incompatible.
//     Type 'string | number' is not assignable to type 'string'.
//       Type 'number' is not assignable to type 'string'.
```

要注意的是:

一旦定義了任意屬性，那麼確定屬性和可選屬性的型別都必須是它的型別的子集

上述的例子報錯了，因為age的value型別是number，number不是string的子集

## 參考文章

https://willh.gitbook.io/typescript-tutorial/basics/type-of-object-interfaces

https://ithelp.ithome.com.tw/articles/10275784