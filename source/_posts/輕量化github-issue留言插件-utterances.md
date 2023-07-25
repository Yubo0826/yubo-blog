---
title: 輕量化github issue留言插件 utterances
date: 2022-07-02 14:44:26
tags:
- utterances
- 套件
categories: Vue
---
## 優點

1.  無廣告
2. 無權限要太多的問題
3. 輕量化
4. Github風格

## 安裝(Vue)

1. 先在你的Github上安裝
    
    https://github.com/apps/utterances
    
2.  在vue專案安裝套件
https://www.npmjs.com/package/vue-utterances
    
    ```jsx
    npm i vue-utterances
    ```
    

## 使用(CLI)

```jsx
<template>
    <vue-utterances repo="khalby786/vue-utterances" theme="github-light" issue-term="pathname" />
</template>

<script>
import VueUtterances from "vue-utterances";

export default {
    ...

    components: {
        VueUtterances
    }

    ...
}
</script>
```

## 屬性說明

| repo | 指定該專案的Github repo，使用者留言都會map在該repo issue裡面 |
| --- | --- |
| theme | 指定留言板風格，詳情見utterances官網 |
| issue-term | 如何指定issue title。我這裡選title， 它會根據網頁title決定issue title |

utterances官網 : 

https://utteranc.es/?installation_id=26169123&setup_action=install

## 參考資料

https://www.jkg.tw/p3350/