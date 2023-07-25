---
title: 'Vue | Markdown轉HTML套件: v-md-editor'
date: 2022-08-17 13:55:39
tags:
- Vue
- 套件
- Markdown
categories: Vue
---
## 文檔

[http://ckang1229.gitee.io/vue-markdown-editor/zh/](https://ckang1229.gitee.io/vue-markdown-editor/zh/#%E4%BB%8B%E7%BB%8D)

## 前言

本來是用`vue3-markdown-it`這個套件，但他產出的HTML版面不太優，後來選擇這個，有Github還有Vue主題可以選，且還有中文文檔。

## 安裝

```jsx
# 使用 npm
npm i @kangc/v-md-editor@next -S

# 使用 yarn
yarn add @kangc/v-md-editor@next
```

### main.js引入

```jsx
import VMdPreview from '@kangc/v-md-editor/lib/preview'
import '@kangc/v-md-editor/lib/style/preview.css'
import vuepressTheme from '@kangc/v-md-editor/lib/theme/vuepress.js'
import '@kangc/v-md-editor/lib/theme/style/vuepress.css'

// 代碼高亮套件 Prism
import Prism from 'prismjs'
// highlight code 針對你需要的語言引入
import 'prismjs/components/prism-javascript'
import 'prismjs/components/prism-cshtml'

VMdPreview.use(vuepressTheme, {
  Prism
})

const app = createApp(App)
app.use(VMdPreview)
```

VMdPreview 是只需要預覽的人使用，官方教學預設是markdown編輯器VMdEditor。

主題可以更換，詳細在官方文件。

另外可以使用一些內建插件，例如:

```jsx
import createLineNumbertPlugin from '@kangc/v-md-editor/lib/plugins/line-number/index'
import createCopyCodePlugin from '@kangc/v-md-editor/lib/plugins/copy-code/index'
import '@kangc/v-md-editor/lib/plugins/copy-code/copy-code.css'
import createEmojiPlugin from '@kangc/v-md-editor/lib/plugins/emoji/index'
import '@kangc/v-md-editor/lib/plugins/emoji/emoji.css'

// 顯示代碼行數
VMdPreview.use(createLineNumbertPlugin())
// 快速複製代碼
VMdPreview.use(createCopyCodePlugin())
// emoji
VMdPreview.use(createEmojiPlugin())
```

## 使用

```html
<v-md-preview :text="aritcle"></v-md-preview>
```

## 注意事項

### 代碼區塊沒有高亮

要注意你的markdown 指定的language有沒有被引入。

例如language是javascript

```html
```js
function aFunc(x){
  function bFunc(){
    console.log( x++ )
  }
  return bFunc
}

const newFunc = aFunc(1)
newFunc()
newFunc()
```
```

如果就需要`import 'prismjs/components/prism-javascript'`