---
title: i18n 優化方案分享(JSON改YAML)
date: 2023-07-31 13:19:57
tags: i18n
categories: i18n
---
## 前言

因為公司有個大專案有多國語系的需求，我找到[Vue I18n](https://kazupon.github.io/vue-i18n/zh/)這個套件，也是官方i18n推薦套件，研究一下，他在小專案是實用的，但在一定規模的專案上，實際運行的話有幾個問題還得考慮

## 實作

### 語言包JS改成YAML

一般的語言包是類JSON形式儲存，並且一個語言就有一個文件

```jsx
// en.js
export default {
	common: {
		all: 'all',
    add: 'Add',
    import: 'Import'
	},
	device: {
		delete: 'Delete',
    lock: 'Lock',
    reboot: 'Reboot'
	}
	// ....
}
```

```jsx
// ja.js
export default {
	common: {
		all: 'すべて',
    add: '追加',
    import: 'インポート'
	},
	device: {
		delete: '削除',
    lock: 'ロックする',
    reboot: '再起動する'
	}
	// ....
}
```

把它集結成一個文件

`.yaml`

```yaml
# lang.yaml
common.all:
  en: All
  ja: すべて
common.add:
  en: Add
  ja: 追加
common.import:
  en: Import
  ja: インポート
device.delete:
  en: Delete
  ja: 削除
device.lock:
  en: Lock
  ja: ロックする
device.reboot:
  en: Reboot
  ja: 再起動する
```

改善的地方:

1. 沒有多餘標點符號，增加可讀性
2. 集結成一個文件，新增修改一個文件下完成
3. 未來新增只要在最下面新增，之後輸出excel，翻譯人員只需到下面尚未翻譯的空格進行翻譯

接著把yaml的內容匯成一個物件，給[Vue I18n](https://kazupon.github.io/vue-i18n/zh/)使用

需安裝套件[yaml-loader](https://www.npmjs.com/package/yaml-loader)

```jsx
// lang/index.js
import translationData from './lang.yaml'

const enObj = {}
const jaObj = {}

Object.entries(translationData).forEach(([key, val]) => {
  const enStr = val.en
  const jaStr = val.ja
  enObj[key] = enStr
  jaObj[key] = jaStr
})

const messages = {
  en: enObj,
  ja: jaObj
}

export default messages
```

```jsx
// main.js

// 通过选项创建 VueI18n 实例
const i18n = new VueI18n({
  locale: 'ja', // 设置地区
  messages, // 设置地区信息
})
```

### 輸出成excel檔案

這裡你也可以自行寫程式完成

也可以用線上轉換工具

https://www.convertcsv.com/newsite/yaml-to-csv.htm

![Untitled](1.png)

拿到excel檔後

我這邊會把excel傳到雲端Google試算表，一來比較方便，一來等到翻譯人員翻好，

要把檔案另存為tsv檔，tsv全名是Tab鍵分隔式檔案，比起csv，它把分隔用的逗號，以tab取代掉

這麼做的原因是翻譯中的文字常有逗號，兩者會產生衝突

外部補充:

[用 TSV 而不用 CSV](https://blog.gslin.org/archives/2020/03/04/9437/%E7%94%A8-tsv-%E8%80%8C%E4%B8%8D%E7%94%A8-csv/)

### tsv 轉成 yaml

由於轉成yaml，線上工具轉換成果都與我要格式有落差，所以自行寫程式完成

```html
<input type="file" accept=".tsv" @change="onFileChange">
```

```jsx
function onFileChange(e) {
  const file = e.target.files[0]
  const reader = new FileReader()
  reader.readAsText(file)
  const self = this
  reader.onload = function() {
    self.tsvToYaml(reader.result)
  }
  reader.onerror = function() {
    console.error(reader.error)
  }
}

function tsvToYaml(content) {
  const lines = content.split('\r\n')
  const headers = lines[0].split('\t').slice(1)
  let yaml = ''
  for (let i = 1; i < lines.length; i++) {
    const row = lines[i].split('\t')
    yaml += `${row[0]}:\n`
    headers.forEach((el, index) => {
      // 檢查有無特殊字元:[
      // 有就包引號""
      let str = row[index + 1]
      let result = /[:[]/.test(str)
      if (result) {
        yaml += `  ${el}: "${row[index + 1]}"\n`
      } else {
        yaml += `  ${el}: ${row[index + 1]}\n`
      }
    })
  }
```

實際運行的話，上傳tsv檔就可以拿到yaml檔，

利用IDE稍微檢查有沒有語法格式錯誤就完成了!