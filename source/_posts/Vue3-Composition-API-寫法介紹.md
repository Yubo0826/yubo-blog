---
title: Vue3 Composition API 寫法介紹
date: 2022-12-30 10:25:29
tags: Vue
categories: Vue
---

## 前言

比較Composition API與Options API的差別

### Options API

在Vue2，編寫組件的方式是options api

**特點:**

在對應的屬性寫對應的功能模塊，比如在data定義數據，在methods定義方法，在computed定義計算屬性。

**缺點:**

1. 當要實現一個功能時，就要把功能的邏輯代碼分散到各個屬性中。
2. 當項目變得龐大，同一功能的邏輯就會變得很散，找起來就相當困難
3. 別人閱讀你得code，變得相對難閱讀

### Composition API

為了解決options api的弊端，Vue3增添setup函數，以解決這種碎片化代碼。

## 入口函式Setup

他會在元件為建立時執行，是使用Composition API的位置。

`setup()`會回傳一個物件，物件的內容都會暴露給元件其餘部分(computed、method、created等)以及元件模版。

```jsx
<script>
  export default {
    setup() {
      const msg = 'hello!';
      const handler = () => {
        alert(msg);
      };
      return { msg, handler };
    },
  };
</script>

<template> 
  <div>
    <p>{{ msg }}</p>
    <button @click="handler">click</button>
  </div>
</template>
```

## 建立響應式資料

上面範例中，`msg`不是響應式，如要建立響應式物件，在Vue3可以用`reactive`和`ref`方法。

### `reactive()`

接受一個普通物件作為參數，回傳一個響應式物件。

該響應式轉換是「深度轉換」，會影響巢狀物件傳遞的所有 property。

```jsx
<script>
  import { reactive } from 'vue';

  export default {
    setup() {
      const data = reactive({
        msg: 'hello!',
      });
      const handler = () => {
        alert(data.msg);
      };
      return { data, handler };
    },
  };
</script>
<template> 
  <div>
    <p>{{ msg }}</p>
    <div>
      <input type="text" v-model="msg">
    </div>
    <button @click="handler">click</button>
  </div>
</template>
```

**補充:**

data option回傳一個物件時，在內部也是透過`reactive()`使其變成響應式。

### `ref()`

接受任何型別的參數，回傳一個響應式且可變得Ref物件，Ref物件只有一個屬性`value`。

**補充:**

1. 若傳入物件型別，內部會呼叫`reactive()`將其轉成響應式物件
2. 從setup回傳得Ref物件在模版讀取是會被自動解開的，不需要在模版中使用`.value`

```jsx
<script>
  import { ref } from 'vue';

  export default {
    setup() {
      const msg = ref('hello!');
      const handler = () => {
        alert(msg.value); // 透過 .value 訪問值
      };
      return { msg, handler };
    },
  };
</script>

<template> 
  <div>
    <p>{{ msg }}</p>
    <div>
      <input type="text" v-model="msg">
    </div>
    <button @click="handler">click</button>
  </div>
</template>
```

## Ref自動解包(unwrap)

**發生時機:** 

將一個ref賦值給一個reactive屬性時，該ref會被自動解包。

```jsx
const count = ref(1)
const obj = reactive({})

obj.count = count

console.log(obj.count) // 1
console.log(obj.count === count.value) // true

const objRef = ref({});
objRef.value.count = count;
console.log(objRef.value.count);  // 1
```

**詳解:**

正常來說，應該要以下的代碼:
`obj.count = count.value`
但因為ref的自動解包，可以變這樣:
`obj.count = count`

**注意:**

當ref物件的value是陣列時，不會自動解包

```jsx
import { ref, reactive } from 'vue';

const arr = ref([]);
arr.value[0] = count;
console.log(arr.value[0]);  // Ref{}
console.log(arr.value[0].value);  // 1
arr.value[1] = count.value;
console.log(arr.value[1]);  // 1

```

## Setup參數

執行 `setup` 函式時，元件實體尚未被建立，在 `setup` 函式內的 `this` 不是實體的引用，若要訪問 `props`、`attrs`、`slots`、`emit` 可以透過 `setup` 函式提供的兩個參數來訪問：

- props
- context

### Props

```jsx
export default {
  props: ['msg'],
  setup(props) {
    console.log(props.msg);
  },
  // ...
};
```

注意:

如果用解構語法`const { msg } = props`，會失去響應性。

因此，Vue3提供`toRefs()`，它會將響應式物件轉成普通物件並將每個 property 轉成指向原始物件相應 property 的 Ref 物件。

```jsx
import { toRef } from 'vue';

export default {
  props: ['msg', 'name'],
  setup(props) {
    const { name, msg } = toRefs(props);
    console.log(name.value, msg.value);
  },
  // ...
};
```