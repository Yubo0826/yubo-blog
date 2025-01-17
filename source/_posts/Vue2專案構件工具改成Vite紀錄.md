---
title: Vue2專案構件工具改成Vite紀錄
date: 2025-01-17 14:54:47
tags:
- 升級紀錄
- Vite
- Vue
categories: Vite
---
## 選擇Vite版本

由於最新Vite 6的Node.js需求是18.x.x以上，專案目前是14.8.0，如果升級到18，會有太多套件有相容性問題，以短期來說，在盡可能不動到套件版本的情況下，選擇了Vite 3，它的最低Node.js是14.18+。我把Node.js環境升級到14.19.1，只有少數套件受到影響。未來時間充裕再全面升級。

另外，Vite對Vue支援的最低版本為2.7，所以要將原2.2升級至2.7，需要更動的地方[官方文件](https://v2.cn.vuejs.org/v2/guide/migration-vue-2-7.html)就很清楚了。

---

升級至的版本：

- Vite：3.2.11
- Node.js：14.19.1
- Vue：2.7

## 下載核心套件

`npm i vite@3`

`npm i @vitejs/plugin-vue2`

## 修改 index.html

需要新增入口JS的引用：

```jsx
<script type="module" src="/src/main.js"></script>
```

## 新增 vite.config.js

在根目錄新增 vite.config.js。

```jsx
import { defineConfig } from 'vite'
import vue2 from '@vitejs/plugin-vue2'
import yaml from '@rollup/plugin-yaml'

export default defineConfig({
  plugins: [vue2(), yaml()],
  server: {
    port: 3000, // 設定開發伺服器的埠號
  },
  resolve: {
    alias: {
      '@': '/src', // 設定路徑別名
    },
  },
})

```

## 修改 package.json

修改指令

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
}
```

## Vite不支援CommonJS require

如還是要使用可以使用插件：

`npm install vite-plugin-commonjs`

並且修改vite.config.js：

```jsx
import commonjs from 'vite-plugin-commonjs'
export default defineConfig({
  plugins: [commonjs()],
});
```

但推薦還是用ESM的import。

## 相容性調整

- **環境變數**：Vite 使用 `.env` 文件處理環境變數，格式為 `VITE_` 開頭。例如：`VITE_API_URL=http://example.com`。
- **兼容插件**：舊的 Webpack 插件需要替換為 Vite 插件。

### yaml loader

下載套件

`import yaml from '@rollup/plugin-yaml'`

並且修改vite.config.js：

```jsx
import yaml from '@rollup/plugin-yaml'

export default defineConfig({
  plugins: [yaml()]
})
```

## 效能差別

|  | Vue CLI | Vite |
| --- | --- | --- |
| 開啟本地伺服器 | 813 毫秒 | 43 秒 |
| 打包速度 | 70 秒 | 18秒 |
| 熱更新 | 3～5 秒 | < 1 秒 |
| 構建體積 | 58.2 MB | 9.9 MB |

## 參考資料

1. https://stackblitz.com/edit/vitejs-vite-ytoabvjm?file=src%2FApp.vue&terminal=dev
2. https://vite.dev/blog/announcing-vite3
3. [**《Vite 基础知识》Vite 不支持 require 解决方案（三种情况/require is not defined）**](https://blog.csdn.net/sinat_31213021/article/details/136401928)
4. [**[Vue] 專案升級之路 — Vite 篇**](https://ceall8650.medium.com/%E5%B0%88%E6%A1%88%E5%8D%87%E7%B4%9A%E4%B9%8B%E8%B7%AF-vite-%E7%AF%87-76a85335b315)