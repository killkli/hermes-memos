---
title: Threads 登入與內容瀏覽
created: 2026-04-28
updated: 2026-04-28
type: concept
tags: [threads, instagram, browser, social-media,雲嘉彰]
sources: [2026-04-28 session]
---

## 概述

Threads 是 Meta 的文字社交平台，帳號與 Instagram 綁定。本頁記錄 Threads 登入流程與內容瀏覽的實戰經驗。

**技能**：`threads-login`（已建立）、`threads-content-browsing`（已建立）

---

## 登入流程

### 認證檔案位置
```
~/.inst_acc
```
Format：
```
USER=你的IG帳號
PASS=你的密碼
```

### 登入步驟

1. 導航到 `threads.net` 登入頁
2. 選擇「使用 Instagram 帳號繼續」
3. 輸入 `~/.inst_acc` 中的 USER（Instagram 用戶名/郵箱/手機）
4. 密碼輸入到同一個輸入框（Threads 有時把密碼欄與用戶名字段合併）
5. 等待 OAuth 重定向完成

---

## 實戰發現的陷阱

### 陷阱 1：Threads 搜尋頁 = 瀏覽器死鎖

嘗試導航到 Threads 內建搜尋頁會觸發 **Meta 防機器人機制**，導致整個瀏覽器會話無法恢復。

**解決方案**：完全避免導航到 `/search` 頁面，改為在首頁滾動收集內容。

### 陷阱 2：密碼欄位有時隱藏

某些 Threads 登入頁佈局下，密碼輸入框初始隱藏，需要先在用戶名字段輸入並觸發 UI 更新後才能看到密碼欄。

### 陷阱 3：Cron 子程序影響瀏覽器狀態

Cron 環境中執行的瀏覽器 session 可能與前台登入狀態衝突，導致空白頁。

**解決方案**：Cron job 中每次都重新完整登入流程，不要依賴既有 session。

---

## 內容過濾偏好（用戶指定）

用戶要求 Threads 報告**只關注**以下兩類內容：

1. **🍽️ 美食/餐廳/在地發現**
   - 餐廳推薦、美食探索、新聞挖掘
   - 範例：彰化員林新開幕橘日鍋物、自助吧 40+ 品項吃到飽

2. **💖 溫馨/正面新聞**
   - 好人好事、社區溫情故事
   - **地區限定**：以雲林、嘉義、彰化（雲嘉彰）為主

**排除**：政治、犯罪新聞、體育、廣告行銷

---

## 瀏覽策略

由於搜尋頁會導致瀏覽器死鎖，改用**首頁滾動收集**方式：

1. 登入後停留在首頁（`threads.net` 或 `threads.net/`）
2. 向下滾動收集多則貼文
3. 從收集的內容中過濾出符合「美食/溫馨」標準的項目
4. 生成繁體中文摘要報告

---

## 關聯技能

- [[threads-login]] — Threads 登入專用技能
- [[threads-content-browsing]] — Threads 內容瀏覽與報告生成技能

