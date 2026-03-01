# Threads API Rate Limit 整理

> 資料來源：https://developers.facebook.com/docs/threads/overview
> 資料來源：https://developers.facebook.com/docs/threads/threads-profiles
> 資料來源：https://developers.facebook.com/docs/threads/retrieve-and-discover-posts/retrieve-posts
> 資料來源：https://developers.facebook.com/docs/threads/keyword-search
> 更新日期：2026-02-27

---

## 1. 應用程式層級 API 呼叫限制

API 呼叫計數是依「應用程式 × 使用者」配對單獨計算：

```
24 小時內允許呼叫次數 = 4800 × 曝光次數 (Impressions)
```

**曝光次數 (Impressions)**：過去 24 小時內，應用程式用戶的 Threads 內容出現在螢幕上的次數。
- 最小值為 **10**（若曝光次數少於 10，預設為 10）

CPU 時間限制：
- `total_cputime` 上限 = `720,000 × 曝光次數`
- `total_time` 上限 = `2,880,000 × 曝光次數`

---

## 2. 發佈貼文限制

| 項目 | 限制 |
|------|------|
| 24 小時內最多貼文數 | **250 則** |
| 計算單位 | 輪播貼文算 1 則 |
| 滾動計算視窗 | 24 小時（86400 秒） |

**查詢目前用量：**
```bash
GET /{threads-user-id}/threads_publishing_limit?fields=quota_usage,config
```

所需權限：`threads_basic` + `threads_content_publish`

**回應範例：**
```json
{
  "data": [{
    "quota_usage": 4,
    "config": {
      "quota_total": 250,
      "quota_duration": 86400
    }
  }]
}
```

---

## 3. 回覆限制

| 項目 | 限制 |
|------|------|
| 24 小時內最多回覆數 | **1,000 次** |
| 滾動計算視窗 | 24 小時（86400 秒） |

**查詢目前用量：**
```bash
GET /{threads-user-id}/threads_publishing_limit?fields=reply_quota_usage,reply_config
```

所需權限：`threads_basic` + `threads_content_publish` + `threads_manage_replies`

**回應範例：**
```json
{
  "data": [{
    "reply_quota_usage": 1,
    "reply_config": {
      "quota_total": 1000,
      "quota_duration": 86400
    }
  }]
}
```

---

## 4. 刪除貼文限制

| 項目 | 限制 |
|------|------|
| 24 小時內最多刪除次數 | **100 次** |
| 滾動計算視窗 | 24 小時（86400 秒） |

**查詢目前用量：**
```bash
GET /{threads-user-id}/threads_publishing_limit?fields=delete_quota_usage,delete_config
```

所需權限：`threads_basic` + `threads_delete`

**回應範例：**
```json
{
  "data": [{
    "delete_quota_usage": 1,
    "delete_config": {
      "quota_total": 100,
      "quota_duration": 86400
    }
  }]
}
```

---

## 5. 地點搜尋限制

| 項目 | 限制 |
|------|------|
| 24 小時內最多地點搜尋次數 | **500 次** |
| 滾動計算視窗 | 24 小時（86400 秒） |

**查詢目前用量：**
```bash
GET /{threads-user-id}/threads_publishing_limit?fields=location_search_quota_usage,location_search_config
```

所需權限：`threads_basic` + `threads_location_tagging`

**回應範例：**
```json
{
  "data": [{
    "location_search_quota_usage": 1,
    "location_search_config": {
      "quota_total": 500,
      "quota_duration": 86400
    }
  }]
}
```

---

## 6. 讀取 Profile 限制

### 自己的帳號（App-Scoped User）

使用 `GET /{threads-user-id}?fields=...` 或 `GET /me?fields=...` 取得自己的個人檔案資訊。

- **無獨立 rate limit**，僅受應用程式層級 API 呼叫限制（4800 × 曝光次數）
- 限制：只能擷取應用程式範圍用戶的個人檔案
- 所需權限：`threads_basic`

### 查詢其他公開帳號（Profile Discovery API）

使用 `GET /profile_lookup?username=...` 查詢其他用戶的公開個人檔案。

| 項目 | 限制 |
|------|------|
| 24 小時內最多查詢次數 | **1,000 次** |
| 滾動計算視窗 | 24 小時 |

所需權限：`threads_basic` + `threads_profile_discovery`

額外限制：
- 只回傳年齡 **18 歲以上**且追蹤者**至少 100 人**的公開帳號
- 使用**標準存取權限**時，只能查詢官方帳號（@meta、@threads、@instagram、@facebook）

---

## 7. 讀取貼文限制

### 自己的帳號貼文

| 端點 | 說明 | Rate Limit |
|------|------|-----------|
| `GET /{threads-user-id}/threads` | 取得自己的貼文列表 | **無獨立限制** |
| `GET /{threads-media-id}` | 取得單一貼文 | **無獨立限制** |

以上端點只受全域應用程式層級限制管控，所需權限：`threads_basic`

### 查詢其他公開帳號的貼文（Profile Discovery API）

使用 `GET /profile_posts?username=...` 取得其他公開帳號的貼文列表。

| 項目 | 限制 |
|------|------|
| 24 小時內最多查詢次數 | **1,000 次** |
| 滾動計算視窗 | 24 小時 |

所需權限：`threads_basic` + `threads_profile_discovery`

額外限制：
- 只回傳年齡 **18 歲以上**且追蹤者**至少 100 人**的公開帳號
- 使用**標準存取權限**時，只能查詢部分官方 Meta 帳號

---

## 8. 關鍵字搜尋限制

使用 `GET /keyword_search` 以關鍵字或主題標籤搜尋公開貼文。

| 項目 | 限制 |
|------|------|
| 24 小時內最多查詢次數 | **2,200 次** |
| 滾動計算視窗 | 24 小時 |

所需權限：`threads_basic` + `threads_keyword_search`

**特別規則（與其他限制不同）：**
- 此限制是**跨所有應用程式**針對同一用戶計算，不因應用程式不同而區分
- 針對**相同關鍵字**的後續查詢也會計入限制
- **未返回任何結果**的查詢不計入此限制
- 包含敏感或冒犯性關鍵字的請求會返回空陣列（不報錯）

---

## 無獨立 Rate Limit 的端點

以下端點**沒有獨立的 rate limit**，僅受全域應用程式層級限制（`4800 × 曝光次數 / 24h`）：

| 端點 | 說明 | 所需權限 |
|------|------|---------|
| `GET /{threads-user-id}/mentions` | 取得被提及的貼文 | `threads_basic` + `threads_manage_mentions` |
| `GET /{threads-media-id}/insights` | 取得貼文洞察報告 | `threads_basic` + `threads_manage_insights` |
| `GET /{threads-user-id}/threads_insights` | 取得用戶洞察報告 | `threads_basic` + `threads_manage_insights` |

---

## 總覽表

| 操作類型 | 端點 | 24 小時限制 | 備註 |
|----------|------|------------|------|
| 發佈貼文 | `POST /{threads-user-id}/threads_publish` | **250 則** | 輪播算 1 則 |
| 回覆 | `POST /{threads-user-id}/threads_publish` | **1,000 次** | — |
| 刪除 | `DELETE /{threads-media-id}` | **100 次** | — |
| 地點搜尋 | `GET /location_search` | **500 次** | — |
| 查詢公開帳號 profile | `GET /profile_lookup` | **1,000 次** | 需 `threads_profile_discovery` |
| 查詢公開帳號貼文 | `GET /profile_posts` | **1,000 次** | 需 `threads_profile_discovery` |
| 關鍵字 / 主題標籤搜尋 | `GET /keyword_search` | **2,200 次** | 跨所有應用程式計算；無結果不計入 |
| 讀取自己的資料 | `GET /me`, `/threads`, `/mentions`, `/insights` 等 | **無獨立限制** | 受全域限制管控 |
| 全域 API 呼叫 | 所有端點 | **4800 × 曝光次數** | 曝光次數最小值為 10 |

所有使用者層級限制的查詢端點統一為：
```
GET /{threads-user-id}/threads_publishing_limit
```
