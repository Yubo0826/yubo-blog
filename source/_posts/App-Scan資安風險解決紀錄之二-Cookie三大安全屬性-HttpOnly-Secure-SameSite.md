---
title: 'App Scan資安風險解決紀錄之二: Cookie三大安全屬性 HttpOnly & Secure & SameSite'
date: 2024-10-08 10:07:17
tags:
- 資安
- App Scan
- Cookie
categories: 資安
---
## 前言

降低Cookie資安風險有很重要的三個屬性，分別是`HttpOnly`、`Secure`以及`SameSite`，如果沒有設置合適的屬性，則會大大增加Cookie資訊招竊的風險。

打開瀏覽器DevTools，在應用程式→Cookie可以看到這三個屬性:

![image.png](1.png)

我在App Scan掃出兩個問題，分別關於`Secure`、`SameSite`。

![Snipaste_2024-09-20_10-14-57.png](2.png)

![Snipaste_2024-09-20_10-02-46.png](3.png)

那麼以下分別介紹這三種屬性的功效:

---

## HttpOnly

用途是讓有設置此屬性的Cookie不能被Javascript讀取，通常需要透過伺服器後端設置。

HttpOnly主要是防範XSS攻擊，因為常見XSS是攻擊者會在網站中植入惡意代碼執行document.cookie，然後獲取被害人的cookie。而設立`HttpOnly`的Cookie因為不能被Javascript讀取，所以可以保護Cookie不被竊取。

## Secure

`Secure`是這三個最為單純的，它的用途是讓開頭不是https的網站都沒有辦法獲得Cookie的資訊。

## SameSite

設置`SameSite`可以限制Cookie是否可以隨跨站請求一起發送，防止跨站請求偽造(CSRF)攻擊。

`SameSite`有三個值可以設置，分別是`Strict`、`Lax`和`None`。下面一一說明:

### 1. Strict

這是最嚴格的選項。Cookie **永遠不會**隨同跨站點請求發送。換句話說，只有在瀏覽器訪問與 Cookie 設置相同的Domain時，Cookie 才會發送。

用例：適用於對安全性要求非常高的場景，例如敏感的身份驗證 Cookie。這樣可以完全阻止第三方網站的請求攜帶此 Cookie。

### 2. Lax

稍微寬鬆的選項。與Strict大概相同，會限制大多數第三方請求，但以下情況例外:

- 跳轉連結 \<a href=”…”\>
- GET表單 \<form method=”GET” action=”…”\>
- 預渲染 \<link rel=”prerender” href=”…”\>

這是最常被設置的選項，因為不像Strict太嚴格又有一定的安全性。

### 3. None

顧名思義就是不限制Cookie傳送，現在瀏覽器為了安全考量，如果Cookie的SameSite設置`None`，那**必須**同時設置 `Secure` 屬性，瀏覽器在https連線下才會傳送。

---

## 實作範例

```jsx
// 前端JS
document.cookie = `account=${account}; SameSite=Lax; Secure;`
```

```java
// 伺服器端
Set-Cookie: CookieName=CookieValue; SameSite=Strict;
```

---

## 結論

- HttpOnly：防止JavaScript讀取Cookie，主要用於防範XSS攻擊⁠。
- Secure：確保Cookie只能通過HTTPS連接傳輸，提高數據傳輸的安全性⁠。
- SameSite：限制Cookie在跨站請求中的傳送，有效防止CSRF攻擊⁠[1](https://www.notion.so/App-Scan-Cookie-HttpOnly-Secure-SameSite-5702e9984c584c3586db18d7daedcc28?pvs=21)。`SameSite`有三個選項： `Strict`：最嚴格，適用於高安全性要求的場景⁠。`Lax`：平衡安全性和功能性，是最常用的選項⁠。`None`：不限制Cookie傳送，但必須同時設置`Secure`屬性⁠。

---

## 參考資料

1. https://tech-blog.cymetrics.io/posts/jo/zerobased-secure-samesite-httponly/
2. https://chatgpt.com/c/66ecec3c-af5c-8011-99db-be6b88f76335