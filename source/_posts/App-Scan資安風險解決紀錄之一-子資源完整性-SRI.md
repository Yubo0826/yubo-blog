---
title: 'App Scan資安風險解決紀錄之一: 子資源完整性 SRI'
date: 2024-10-08 10:06:59
tags:
- 資安
- App Scan
- SRI
categories: 資安
---
## 前言

子資源完整性 (Subresource Integrity, SRI)，是一種瀏覽器的安全特性，確保”**外部**”來源加載的內容在傳輸過程中沒有被竄改。

如CDN獲得的資源，如果攻擊者掌握了對CDN的控制權，就可以將惡意內容散播到CDN的文件內，從而攻擊使用這個資源的用戶。

子資源完整性能確保減輕遭遇此攻擊的風險，確保外部資源沒有被第三方做任何修改。

---

## SRI工作原理

1. 你先計算文件的Hash值。
2. 將Hash值放入資源的integrity中。
3. 瀏覽器加載資源後會計算它的Hash值，並和integrity的值比較，如果一樣，資源會被加載，否則，加載會停止。

---

## SRI如何使用

使用SRI方法是，在所有`script`和`link`有使用到外部資源的元素，加入`integrity`屬性，其值為資源URL的加密hash值。

> 生成SRI hash的工具可以用:
[SRI Hash Generator](1.png)
> 

例如:

原本的

```jsx
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.25.0/axios.min.js"></script>
```

將URL帶給Generator後，就可以拿到

![image.png](1.png)

把新的`<script>`取代舊的`<script>`即可。

---

## 結論

要降低這個風險並不困難，在外部連結添加integrity屬性就好。