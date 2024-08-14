---
title: Vue2 響應式原理(defineProperty)
date: 2023-10-05 17:49:02
tags: 
- Vue
- 源碼
categories: Vue
---
Vue2 響應式原理與Javascript ES5的`Object.defineProperty`息息相關，透過defineProperty控制data選項的property，攔截get、set動作，追蹤資料的變化同時更新視圖。

## Vue2如何追蹤變化

迭代data option的對象，通過`Object.defineProperty`重新設置對象的屬性，使能夠捕獲到該屬性的set/get值，當property發生變化，就會去通知更新視圖。

相關源碼:

https://github.com/vuejs/vue/blob/main/src/core/observer/index.ts#L48

```jsx
// 迭代對象property
const keys = Object.keys(value)
for (let i = 0; i < keys.length; i++) {
  const key = keys[i]
  defineReactive(value, key, NO_INITIAL_VALUE, undefined, shallow, mock)
}
```

```jsx
// 擷取defineReactive方法內容
// 為property新增getter&setter
function defineReactive(
  obj: object,
  key: string,
  val?: any,
  customSetter?: Function | null,
  shallow?: boolean,
  mock?: boolean
) {
// ...
Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      // ...
      if (Dep.target) {
        // 收集依賴
        dep.depend()
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      // ...
      // 通知視圖更新
      dep.notify()
    }
  })
}

```

## Vue2 產生變化但不會更新視圖的情況

### 新增屬性

defineProperty 有一個缺點，就是它是針對對象的屬性，如果該對象新增一個屬性，那就無法觀察新屬性的get&set行為。

例如按了按鈕，obj物件新增屬性b，但view沒有更新:

```jsx
<div id="app">
	<button>test</button>
	{{ obj.a }} 、 {{ obj.b }}
</div>
 
<script>
  var app = new Vue({
	  el: '#app',
	  data: {
			message: 'Hello Vue!',
			obj: {
				a: 123
			}
	  },
	  method: {
			test() {
				this.obj.b = 456
			}
	  }
	})
</script>
```

而Vue2有提供解決方案:

`Vue.set(object, propertyName, value)`

set方法會手動的為該對象的屬性作觀察

下方為set源碼:

```jsx
function set (target: Array<any> | Object, key: any, val: any): any {
  // ....
  if (!ob) {
    target[key] = val
    return val
  }
  defineReactive(ob.value, key, val)  // <----
  ob.dep.notify()
  return val
}
```

所以上面範例要改成這樣，視圖才會更新:

```jsx
this.obj.b = 456  // X

this.$set(this.obj, 'b', 456)  // O
```

*補充:*

而Vue3用proxy，因為proxy是針對對象，而非對象的屬性，所以新增屬性會觸發setter

```jsx
const origin = {}
const obj = new Proxy(origin, {
  get: function (obj, key) {
        console.log(obj[key])
  },
  set: function (obj, key, value) {
      console.log('obj[key]:' + value)
      obj[key] = value
      return true
  }
});

obj.a = 6  // obj[key]:6
obj.a  // 6
```

### 陣列變化

用範例看:

```jsx
<div>{{ this.arr[1] }}</div>
<div>{{ this.arr[2] }}</div>

<script>
var app = new Vue({
	  el: '#app',
	  data: {
			arr: [1,2,3]
	  },
	  methods: {
			test1() {
				this.arr[2] = 5
				this.arr[3] = 7
			},
			test2() {
				this.arr = [7,8,9,10]
			}
	  }
})
</script>
```

執行test1，view沒有改變

執行test2，view會有改變

Vue2不能偵測兩種陣列變化:

1. 直接設值，`vm.items[indexOfItem] = newValue`
2. 有修改到陣列變化，`vm.items.length = newLength`

解決方法同樣也是:

Vue.set(vm.items, indexOfItem, newValue)

另外:

Javascript常見的原生陣列方法，例如splice、filter，有特別對這些方法做觀察，所以用這些方法改變陣列，是可以同步更新視圖的。