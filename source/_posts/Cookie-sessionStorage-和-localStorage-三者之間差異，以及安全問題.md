---
title: Cookie, sessionStorage 和 localStorage 三者之間差異，以及安全問題
date: 2024-09-12 10:00:26
tags: 
- 資安
- 瀏覽器
- Cookie
categories:
- 資安
---
## 前言

Cookie、sessionStorage 和 localStorage 是三種主要的用戶端瀏覽器數據存儲方式，各有其獨特的特點和適用場景。儘管我經常使用這些技術，在開發過程中選擇最適合的方式仍然有些困惑。因此，我決定整理這篇文章，以釐清它們之間的差異。

---

以下為三種儲存方式的簡介:

## Cookie

當使用者瀏覽器首次發送請求時，伺服器會在回應的標頭中添加一個 **`Set-Cookie`** 選項，並將 cookie 放入回應中。

```
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

這個 cookie 會被送回並儲存在使用者的瀏覽器中。之後，每當使用者瀏覽器發送請求時，這個 cookie 都會被自動攜帶並發送回伺服器。

```
GET /sample_page.html HTTP/2.0
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

Cookie 容量小（4KB），可設置過期時間，每次 HTTP 請求都會攜帶，常用於身份驗證和用戶偏好設置。

```
Set-Cookie: id=a3fWa; Expires=Thu, 31 Oct 2021 07:28:00 GMT;
```

其他Cookie的設置與資安防禦請參考[MDN](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Cookies)。

## **Web Storage API**

Web Storage API 是一種以鍵值對（key-value）方式儲存資料的機制，且不會隨瀏覽器請求發送。它包含 sessionStorage 和 localStorage 兩種機制，其儲存容量比 cookie 大得多，通常在 5MB 到 10MB 之間。

### sessionStorage (工作階段儲存空間)

為每個來源（origin）提供一個獨立的儲存區，該儲存區在關閉頁面時即被清除。適用於暫時性資料儲存和單頁應用程式（SPA）狀態管理。

```jsx
// sessionStorage 存入數據
sessionStorage.setItem("name", "David");

// sessionStorage 讀取數據
sessionStorage.getItem("name");
```

### localStorage (本地儲存空間)

與 sessionStorage 功能類似，但數據即使在關閉頁面或瀏覽器後仍然存在。這些數據只能通過 JavaScript、清除快取或使用網頁開發工具來刪除。localStorage 適用於長期存儲用戶偏好和離線應用數據。

```jsx
// localStorage 存入數據
localStorage.setItem("name", "David");

// localStorage 讀取數據
localStorage.getItem("name");
```

---

## 三者之間的主要差異

| 特性 | Cookie | sessionStorage | localStorage |
| --- | --- | --- | --- |
| 容量 | 4KB | 5MB | 5MB+ |
| 生命週期 | 可設置 | 會話期間 | 永久 |
| 與服務器通信 | 是 | 否 | 否 |
| 訪問限制 | 所有窗口 | 同源同窗口 | 同源所有窗口 |

---

## 安全問題及注意事項

### Cookie 安全問題

- CSRF（跨站請求偽造）攻擊風險
- XSS（跨站腳本）攻擊可能竊取 cookie
- 建議使用 HttpOnly 和 Secure 標誌增強安全性

### sessionStorage 和 localStorage 安全問題

- XSS 攻擊可能訪問存儲的數據
- 不要存儲敏感信息
- 考慮對存儲的數據進行加密

### 通用安全建議

- 實施內容安全策略（CSP）
- 定期清理不必要的數據
- 對用戶輸入進行嚴格驗證和過濾

---

## 結論

cookie、sessionStorage 和 localStorage 各有其優缺點和適用場景。在選擇使用哪種存儲方式時，開發者需要考慮數據的生命週期、容量需求以及安全性要求。無論選擇哪種方式，都應該謹慎處理敏感信息，並採取適當的安全措施來保護用戶數據。

---

## 參考資料

1. https://www.explainthis.io/zh-hant/swe/cookie-sessionstorage-localstorage-difference
2. https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API
3. https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Cookies