# Threads API - Profile & Posts 端點文件

> 來源：
> - https://developers.facebook.com/docs/threads/threads-profiles
> - https://developers.facebook.com/docs/threads/retrieve-and-discover-posts/retrieve-posts

---

## 1. Threads 個人檔案 API（Profile API）

### 端點

```
GET /{threads-user-id}?fields=...
```

擷取應用程式範圍用戶的個人檔案資訊。

### 權限

| 權限 | 說明 |
|------|------|
| `threads_basic` | 對所有 Threads API 端點進行任何呼叫時的必要項目 |

### 限制

- 只能擷取應用程式範圍用戶的個人檔案。

### 可用欄位（Fields）

| 名稱 | 類型 | 說明 |
|------|------|------|
| `id` | 字串 | Threads 用戶編號。預設會傳回此編號。 |
| `username` | 字串 | Threads 上的用戶名稱或專屬用戶名稱。 |
| `name` | 字串 | 用戶在 Threads 上的顯示名稱。 |
| `threads_profile_picture_url` | 字串 | 用戶在 Threads 上的大頭貼照網址。 |
| `threads_biography` | 字串 | Threads 個人檔案上的個人簡介文字。 |
| `is_verified` | 布林值 | 如果用戶已在 Threads 通過驗證，則傳回 `true`。 |

### 要求範例

```bash
curl -s -X GET \
  "https://graph.threads.net/v1.0/me?fields=id,username,name,threads_profile_picture_url,threads_biography,is_verified&access_token=<ACCESS_TOKEN>"
```

### 回覆範例

```json
{
  "id": "1234567",
  "username": "threadsapitestuser",
  "name": "Threads API Test User",
  "threads_profile_picture_url": "https://scontent-sjc3-1.cdninstagram.com/link/to/profile/picture/on/threads/",
  "threads_biography": "This is my Threads bio.",
  "is_verified": false
}
```

---

## 2. Threads 個人檔案探索 API（Profile Lookup API）

### 端點

```
GET /profile_lookup?username=...
```

查詢公開個人檔案並擷取其基本個人檔案資訊。

### 權限

| 權限 | 說明 |
|------|------|
| `threads_basic` | 對所有 Threads API 端點進行任何呼叫時的必要項目 |
| `threads_profile_discovery` | 對所有 Threads 個人檔案探索 API 端點進行任何呼叫時的必要項目 |

> 使用**標準存取權限**時，只能查詢部分官方 Meta 帳號（@meta、@threads、@instagram、@facebook）。

### 限制

- 只傳回年齡超過 18 歲且追蹤者至少 100 人的公開個人檔案。
- 用戶在連續 24 小時期間內，最多可以傳送 **1,000 次**要求。查詢一旦送出，即計入此 24 小時的限制。

### 請求參數（Parameters）

| 名稱 | 類型 | 必要 | 說明 |
|------|------|------|------|
| `access_token` | 字串 | 是 | Threads 圖形 API 用戶存取權杖。 |
| `username` | 字串 | 是 | Threads 上的用戶名稱或專屬用戶名稱。必須完全相符。 |

### 可用欄位（Fields）

| 名稱 | 類型 | 說明 |
|------|------|------|
| `username` | 字串 | Threads 上的用戶名稱或專屬用戶名稱。 |
| `name` | 字串 | 用戶在 Threads 上的顯示名稱。 |
| `profile_picture_url` | 字串 | 用戶在 Threads 上的大頭貼照網址。 |
| `biography` | 字串 | Threads 個人檔案上的個人簡介文字。 |
| `follower_count` | 整數 | 用戶的追蹤者總數。 |
| `likes_count` | 整數 | 用戶貼文過去 7 天的按讚次數。 |
| `quotes_count` | 整數 | 用戶貼文過去 7 天的引用次數。 |
| `reposts_count` | 整數 | 用戶貼文過去 7 天的轉發次數。 |
| `views_count` | 整數 | 用戶貼文過去 7 天的檢視次數。 |
| `is_verified` | 布林值 | 如果用戶已在 Threads 通過驗證，則傳回 `true`。 |

### 要求範例

```bash
curl -i -X GET \
  "https://graph.threads.net/v1.0/profile_lookup?access_token=<ACCESS_TOKEN>&username=<THREADS_USERNAME>"
```

### 回覆範例

```json
{
  "username": "meta",
  "name": "Meta",
  "profile_picture_url": "https://scontent-sjc3-1.cdninstagram.com/link/to/profile/picture/on/threads/",
  "biography": "Connect with what you love to make things happen. It's Your World.",
  "is_verified": true,
  "follower_count": 1234567,
  "likes_count": 1234567,
  "quotes_count": 1234567,
  "replies_count": 1234567,
  "reposts_count": 1234567,
  "views_count": 1234567
}
```

---

## 3. 應用程式範圍用戶貼文 API（User Threads API）

### 端點

```
GET /{threads-user-id}/threads
```

傳回用戶建立之所有串文的分頁清單。

### 權限

| 權限 | 說明 |
|------|------|
| `threads_basic` | 對所有 Threads API 端點進行任何呼叫時的必要項目 |

### 限制

- 只能擷取應用程式範圍用戶建立之所有串文的分頁清單。

### 可用欄位（Fields）

| 名稱 | 類型 | 說明 |
|------|------|------|
| `id` | 字串 | 影音內容的編號（預設傳回）。 |
| `media_product_type` | 字串 | 發佈影音內容的介面。Threads 貼文值為 `THREADS`。 |
| `media_type` | 字串 | 影音內容類型：`TEXT_POST`、`IMAGE`、`VIDEO`、`CAROUSEL_ALBUM`、`AUDIO` 或 `REPOST_FACADE`。含 GIF 或票選活動的貼文傳回 `TEXT_POST`。 |
| `media_url` | 字串 | 貼文的影音內容網址。 |
| `permalink` | 字串 | 貼文的永久連結。含著作權素材或違反著作權時忽略。 |
| `owner` | 物件 | 建立貼文的 Threads 用戶編號。僅適用於您擁有的頂層貼文。 |
| `username` | 字串 | 建立貼文的 Threads 用戶名稱。 |
| `text` | 字串 | Threads 貼文的文字內容。 |
| `timestamp` | 字串 | 貼文發佈時間（ISO 8601 格式）。 |
| `shortcode` | 字串 | 影音內容的簡碼。 |
| `thumbnail_url` | 字串 | 縮圖網址。僅顯示於含影片的 Threads 影音內容。 |
| `children` | 物件 | 子貼文清單。僅顯示於輪播貼文。 |
| `is_quote_post` | 布林值 | 指出影音內容是否為引用的貼文。 |
| `quoted_post` | 物件 | 所引用貼文的影音內容編號。僅顯示在引用貼文上。 |
| `reposted_post` | 物件 | 已轉發貼文的影音內容編號。僅顯示在轉發貼文上。 |
| `alt_text` | 字串 | 圖像或影片的無障礙環境文字標籤或描述。 |
| `link_attachment_url` | 字串 | 附加在貼文上的網址。 |
| `gif_url` | 字串 | 貼文附加 GIF 的網址。僅顯示在有 GIF 附件的貼文上。 |
| `poll_attachment` | 物件 | 貼文的票選活動附件。僅顯示在有票選活動的貼文上。 |
| `topic_tag` | 字串 | 貼文標頭的主題標籤。僅顯示在有主題標籤的貼文上。 |
| `is_spoiler_media` | 布林值 | 表示發佈的影音內容物件是否會劇透。 |
| `text_entities` | 物件 | 表示貼文的文字欄位是否會劇透。 |
| `text_attachment` | 物件 | 貼文的文字附件。 |
| `ghost_post_status` | 字串 | 限時貼文的狀態：`ACTIVE`（目前）或 `ARCHIVED`（過期）。僅針對限時貼文顯示。 |
| `ghost_post_expiration_timestamp` | 字串 | 限時貼文的到期時間戳記（ISO 8601 格式）。僅在限時貼文案例中顯示。 |
| `is_verified` | 布林值 | 如果貼文作者的個人檔案已在 Threads 通過驗證，則傳回 `true`。 |
| `profile_picture_url` | 字串 | 貼文作者在 Threads 上的大頭貼照網址。 |

### 要求範例

```bash
curl -s -X GET \
  "https://graph.threads.net/v1.0/me/threads?fields=id,media_product_type,media_type,media_url,permalink,owner,username,text,topic_tag,timestamp,shortcode,thumbnail_url,children,is_quote_post&since=2023-10-15&until=2023-11-18&limit=1&access_token=<ACCESS_TOKEN>"
```

### 回覆範例

```json
{
  "data": [
    {
      "id": "1234567",
      "media_product_type": "THREADS",
      "media_type": "TEXT_POST",
      "permalink": "https://www.threads.net/@threadsapitestuser/post/abcdefg",
      "owner": {
        "id": "1234567"
      },
      "username": "threadsapitestuser",
      "text": "Today Is Monday",
      "topic_tag": "Mondays",
      "timestamp": "2023-10-17T05:42:03+0000",
      "shortcode": "abcdefg",
      "is_quote_post": false
    }
  ],
  "paging": {
    "cursors": {
      "before": "BEFORE_CURSOR",
      "after": "AFTER_CURSOR"
    }
  }
}
```

> **分頁說明：** 回應含有多頁資料時，會包含 `before` 和 `after` 游標。需手動使用這些游標建構 previous/next 查詢字串。

---

## 4. 公開個人檔案貼文 API（Profile Posts API）

### 端點

```
GET /profile_posts?username=...
```

查詢公開個人檔案並擷取其在 Threads 上的貼文分頁清單。

### 權限

| 權限 | 說明 |
|------|------|
| `threads_basic` | 對所有 Threads API 端點進行任何呼叫時的必要項目 |
| `threads_profile_discovery` | 對所有 Threads 個人檔案探索 API 端點進行任何呼叫時的必要項目 |

> 使用**標準存取權限**時，只能擷取部分官方 Meta 帳號的貼文（@meta、@threads、@instagram、@facebook）。

### 限制

- 只傳回年齡超過 18 歲且追蹤者至少 100 人的公開個人檔案。
- 用戶在連續 24 小時期間內，最多可以傳送 **1,000 次**要求。查詢一旦送出，即計入此 24 小時的限制。

### 請求參數（Parameters）

| 名稱 | 類型 | 必要 | 說明 |
|------|------|------|------|
| `access_token` | 字串 | 是 | Threads 圖形 API 用戶存取權杖。 |
| `username` | 字串 | 是 | Threads 上的專屬用戶名稱。必須完全相符。 |

### 可用欄位（Fields）

與[應用程式範圍用戶貼文](#3-應用程式範圍用戶貼文-api（user-threads-api）)相同的欄位，但**不包含** `owner` 欄位。

### 要求範例

```bash
curl -i -X GET \
  "https://graph.threads.net/v1.0/profile_posts?access_token=<THREADS_TESTER_ACCESS_TOKEN>&username=<THREADS_USERNAME>&fields=id,media_product_type,media_type,media_url,permalink,username,text,topic_tag,timestamp,shortcode,thumbnail_url,children,is_quote_post&since=2023-10-15&until=2023-11-18&limit=1"
```

### 回覆範例

```json
{
  "data": [
    {
      "id": "1234567",
      "media_product_type": "THREADS",
      "media_type": "TEXT_POST",
      "permalink": "https://www.threads.net/@meta/post/abcdefg",
      "username": "meta",
      "text": "Today Is Monday",
      "topic_tag": "Mondays",
      "timestamp": "2023-10-17T05:42:03+0000",
      "shortcode": "abcdefg",
      "is_quote_post": true
    }
  ],
  "paging": {
    "cursors": {
      "before": "BEFORE_CURSOR",
      "after": "AFTER_CURSOR"
    }
  }
}
```

---

## 5. 單一 Threads 影音內容物件

### 端點

```
GET /{threads-media-id}
```

傳回單一 Threads 影音內容物件。

### 權限

| 權限 | 說明 |
|------|------|
| `threads_basic` | 對所有 Threads API 端點進行任何呼叫時的必要項目 |

> 尚未獲得進階存取權限時，只能擷取由 Threads 測試人員建立的貼文。

### 可用欄位（Fields）

與[應用程式範圍用戶貼文](#3-應用程式範圍用戶貼文-api（user-threads-api）)相同的欄位。

### 要求範例

```bash
curl -s -X GET \
  "https://graph.threads.net/v1.0/<THREADS_MEDIA_ID>?fields=id,media_product_type,media_type,media_url,permalink,owner,username,text,topic_tag,timestamp,shortcode,thumbnail_url,children,is_quote_post&access_token=<ACCESS_TOKEN>"
```

### 回覆範例

```json
{
  "id": "1234567",
  "media_product_type": "THREADS",
  "media_type": "TEXT_POST",
  "permalink": "https://www.threads.net/@threadsapitestuser/post/abcdefg",
  "owner": {
    "id": "1234567"
  },
  "username": "meta",
  "text": "Today Is Monday",
  "topic_tag": "Mondays",
  "timestamp": "2023-10-09T23:18:27+0000",
  "shortcode": "abcdefg",
  "is_quote_post": false
}
```
