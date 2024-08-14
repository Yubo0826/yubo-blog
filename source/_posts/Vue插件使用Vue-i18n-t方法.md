---
title: Vue插件使用Vue i18n $t方法 exception Cannot read properties of undefined (reading '_t')”
date: 2023-09-11 17:43:52
tags:
- BugFix
- i18n
- Vue
categories: Vue
---
## 緣由

在專案自製一個popup視窗插件，上面的文字用i18n模板，但使用上會產生錯誤，

詳看log後，發現調用i18n方法`$t`，會報錯，而錯誤似乎是源於i18n實例找不到

```jsx
Cannot read properties of undefined (reading '_t')"
// _t指向i18n實例
```

i18n實例在main.js裡面被定義

```jsx
// i18n配置
import VueI18n from 'vue-i18n'
import messages from './lang/index'

Vue.use(VueI18n)  // 可以使用$t
const lang = 'en'
const i18n = new VueI18n({
  locale: lang,
  messages
})

// 引入自己的插件
import MsgBox from './components/common/MessageBox'
Vue.use(MsgBox)

// Vue實例產生
new Vue({
  el: '#root',
  i18n,
  router: router,
  store: store,
  render: h => h(AppView)
})
```

## 解決

這個i18n實例包含翻譯語言包與預設語言等資訊，是$t所需的，但原來的flow，`Vue.use(MsgBox)`這時找不到i18n實例，因為等`new Vue()`才能拿到。

現行的解決方法是`Vue.use(MsgBox)`在`new Vue()`在後才調用

改成:

```jsx
// main.js
const VueInstance = new Vue({
  el: '#root',
  i18n,
  router: router,
  store: store,
  render: h => h(AppView)
})

Vue.use(MsgBox, { i18n: VueInstance.$i18n })
```

把i18n實例傳入插件裡面

```jsx
// MessageBox.js
import MessageBoxComp from './MessageBox.vue'

const msgBox = {
  install: (Vue, { i18n }) => {
    const MsgBoxConstructor = Vue.extend(MessageBoxComp)
    const instance = new MsgBoxConstructor()
    instance.$mount(document.createElement('div'))

    Vue.prototype.$msg = {}

    Vue.prototype.$msg.error = (msg, title) => {
      document.body.appendChild(instance.$el)
      instance.type = 'error'
      instance.show = true
      instance.i18n = i18n.locale
    }
    Vue.prototype.$msg.success = (msg) => {
      document.body.appendChild(instance.$el)
      instance.type = 'success'
      instance.show = true
      instance.i18n = i18n.locale
      instance.msg = msg
    }
  }
}

export default msgBox
```

雖然這樣可以成功除錯，但在Vue官方文件明確表示，`Vue.use`需要在調用`new Vue()`前調用，確實這樣是非常合理的，但在專案裡還是可以正常使用插件，詳細原因不得而知。

https://v2.cn.vuejs.org/v2/api/#Vue-use

## 補充

官方使用/開發插件文件

https://v2.cn.vuejs.org/v2/guide/plugins.html