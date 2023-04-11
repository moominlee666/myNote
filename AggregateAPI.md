# Aggregate API 文件

## 接入資訊
1. 請先取得代理商API Token
2. Requst/Response均為JSON格式，編碼為 UTF-8。
3. 所有API請求Method均為POST
4. API request sample
```http
POST /api/CreatePlayer HTTP/1.1
Host: aggregate.gamejar.xyz
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImN0eSI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6ImFwOSIsImh0dHA6Ly9zY2hlbWFzLm1pY3Jvc29mdC5jb20vd3MvMjAwOC8wNi9pZGVudGl0eS9jbGFpbXMvcm9sZSI6ImFnZW50IiwianRpIjoiMGU1OTk1MmYtNjg1Zi00ZTgyLTg0YWYtNTkwZjVmYTgxODQ2IiwiaHR0cDovL3NjaGVtYXMubWljcm9zb2Z0LmNvbS93cy8yMDA4LzA2L2lkZW50aXR5L2NsYWltcy9ncm91cHNpZCI6IkFHRU5UIiwiZXhwIjoxOTk1NjExMTg1LCJpc3MiOiJHYW1lSmFyLmNvbSIsImF1ZCI6IkdhbWVKYXIifQ.UTpvqUrYfWdr9Su5hLCCe8E-Zm6tpBOyCp1N8sCNfiM
Content-Type: application/json

{
  "id": "player001",
  "nickname": "player001",
  "currency": "CNY"
}
```

## 登入遊戲流程

```sequence
使用者前台->包網站台: 登入遊戲
包網站台->GameJarAPI: CreatePlayer
GameJarAPI -> GameJarAPI : 建立gamejar player
GameJarAPI-->包網站台: success
包網站台->GameJarAPI: Deposit
GameJarAPI -> GameJarAPI : 更新Player餘額
GameJarAPI-->包網站台: success
包網站台->GameJarAPI: Login game
GameJarAPI-->包網站台: game url
包網站台 --> 使用者前台: 返回遊戲連結
使用者前台 --> 使用者前台 : 開啟遊戲
```

## 建立使用者

建立GameJar使用者

請求URL
```
https://<url>/api/CreatePlayer [POST]
```

請求內容 Request body

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| id     | string     | 3-20     | Y | 只接受 A-Z, a-z, 0-9, _
| nickname     | string     | 3-20     | N | 只接受 A-Z, a-z, 0-9, _ 預設與id同
| sub_agent_id     | string     | 3-10 | N | 只接受 A-Z, a-z, 0-9, _ 預設與agent id相同

回傳內容 Response body

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| code     | int | 參考錯誤代碼表
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
    "code": 0,
    "message": "success",
    "timestamp": 1681117805889
}
```
