# GameJar Aggregate API 文件 v1.0.0.1
## 目錄
[TOC]
## 版本記錄
| 日期 | 版號 | 修改內容 |
| -------- | -------- | -------- |
| 2023-04-14     | v1.0.0.1     | 初版     |


## 接入資訊
1. 請先取得代理商API Token
2. Requst/Response均為JSON格式，編碼為 UTF-8。
3. 所有API請求Method均為POST
4. API request sample
### HTTP sample
```http
POST /api/CreatePlayer HTTP/1.1
Host: aggregate.gamejar.cc
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImN0eSI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6ImFwOSIsImh0dHA6Ly9zY2hlbWFzLm1pY3Jvc29mdC5jb20vd3MvMjAwOC8wNi9pZGVudGl0eS9jbGFpbXMvcm9sZSI6ImFnZW50IiwianRpIjoiMGU1OTk1MmYtNjg1Zi00ZTgyLTg0YWYtNTkwZjVmYTgxODQ2IiwiaHR0cDovL3NjaGVtYXMubWljcm9zb2Z0LmNvbS93cy8yMDA4LzA2L2lkZW50aXR5L2NsYWltcy9ncm91cHNpZCI6IkFHRU5UIiwiZXhwIjoxOTk1NjExMTg1LCJpc3MiOiJHYW1lSmFyLmNvbSIsImF1ZCI6IkdhbWVKYXIifQ.UTpvqUrYfWdr9Su5hLCCe8E-Zm6tpBOyCp1N8sCNfiM
Content-Type: application/json

{
  "id": "player001",
  "nickname": "player001",
  "currency": "CNY"
}
```

### cURL sample
```curl!
curl --location --request POST 'https://aggregate.gamejar.cc/api/CreatePlayer' \
--header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImN0eSI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6ImFwOSIsImh0dHA6Ly9zY2hlbWFzLm1pY3Jvc29mdC5jb20vd3MvMjAwOC8wNi9pZGVudGl0eS9jbGFpbXMvcm9sZSI6ImFnZW50IiwianRpIjoiMGU1OTk1MmYtNjg1Zi00ZTgyLTg0YWYtNTkwZjVmYTgxODQ2IiwiaHR0cDovL3NjaGVtYXMubWljcm9zb2Z0LmNvbS93cy8yMDA4LzA2L2lkZW50aXR5L2NsYWltcy9ncm91cHNpZCI6IkFHRU5UIiwiZXhwIjoxOTk1NjExMTg1LCJpc3MiOiJHYW1lSmFyLmNvbSIsImF1ZCI6IkdhbWVKYXIifQ.UTpvqUrYfWdr9Su5hLCCe8E-Zm6tpBOyCp1N8sCNfiM' \
--header 'Content-Type: application/json' \
--data-raw '{
  "id": "player001"",
  "nickname": "player001"
}'
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

## API

### API 請求頻率限制


| 方法 | 限制秒數 | 限制對象 |
| -------- | -------- | -------- |
| LoginGame     | 5     | Player     |
| LogoutGame    | 5     | Player     |
| Deposit    | 1     | Player     |
| Withdraw    | 3     | Player     |
|GetBetRecordsByTimePeriod|3|Agent|
|GetBetRecordsByVersionKey|3|Agent|
|GetHourlyReport|20|Agent|

### 建立使用者

建立GameJar使用者

**請求URL**
```
https://<url>/api/CreatePlayer [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| id     | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _
| nickname     | string     | 3-20     | N | 接受 A-Z, a-z, 0-9, - , _ 預設與id同
| sub_agent_id     | string     | 3-10 | N | 接受 A-Z, a-z, 0-9, - , _ 預設與agent id相同
```json!
{
  "id": "player0001",
  "nickname": "player001",
  "sub_agent_id": "try"
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
    "code": 0,
    "message": "success",
    "timestamp": 1681117805889
}
```
### 查詢使用者

查詢GameJar使用者

**請求URL**
```
https://<url>/api/GetPlayer [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| player_id     | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _ |
```json!
{
  "player_id": "player0001"
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|player_id|string| 玩家id|
|nickname|string| 玩家暱稱|
|sub_agnet|string| 子代理|
|currency|string|貨幣 ISO 4217|
| enable     | bool | 是否停用|
| total_credit     | decimal | 所有錢包總額|
| wallet     | array | 請參考wallet data|
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)|
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 

**回傳內容 wallet data**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|wallet|decimal| 錢包名稱|
|credit|decimal| 錢包餘額|
| code     | int | 參考錯誤代碼表|
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 

```json!
{
    "data": {
        "player_id": "player0001",
        "nickname": "player0001",
        "sub_agnet": "try",
        "currency": "THB",
        "enable": true,
        "total_credit": 197.5300,
        "wallet": [
            {
                "wallet": "GAMEJAR",
                "credit": 0.0000,
                "code": 0,
                "message": "success",
                "timestamp": 1681270462951
            },
            {
                "wallet": "JDB",
                "credit": 197.53,
                "code": 0,
                "message": "success",
                "timestamp": 1681270462982
            }
        ]
    },
    "code": 0,
    "message": "success",
    "timestamp": 1681270461201
```


### 存款

將額度轉入GameJar錢包

**請求URL**
```
https://<url>/api/Deposit [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| player_id    | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _ |
| transaction_id     | string     | 40     | Y | 轉帳id唯一值, 接受 A-Z, a-z, 0-9, - , _ |
| amount     | decimal     | 最大 100,000,000 | Y | 轉入額度, 接受2個小數位的正數

```json!
{
  "player_id": "player0001",
  "transaction_id": "txn230329182045487",
  "amount": 999.99
}
```

**回傳內容 Response body**
:::warning
回傳內容 code = 301 表示有重複轉帳id, 不適用轉帳查詢功能 可直接視為轉帳失敗
:::
| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|transaction_id|string|轉帳id|
|player_id|string| 玩家id|
|amount|decimal| 轉入額度|
|balance_before|decimal| 轉入前GameJar錢包餘額|
|balance_after|decimal| 轉入後GameJar錢包餘額|
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
    "data": {
        "transaction_id": "txn230329182045487",
        "player_id": "player0001",
        "amount": 999.99,
        "balance_before": 0.0000,
        "balance_after": 999.9900
    },
    "code": 0,
    "message": "success",
    "timestamp": 1681269619640
}
```
### 提款

將額度從GameJar錢包轉出


**請求URL**
```
https://<url>/api/Withdraw [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| player_id    | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _ |
| transaction_id     | string     | 40     | Y | 轉帳id唯一值, 接受 A-Z, a-z, 0-9, - , _ |
| amount     | decimal     | 最大 100,000,000 | Y | 轉入額度, 接受2個小數位的正數

```json!
{
  "player_id": "player0001",
  "transaction_id": "txn230329182045488",
  "amount": 999.99
}
```

**回傳內容 Response body**
:::warning
回傳內容 code = 301 表示有重複轉帳id, 不適用轉帳查詢功能 可直接視為轉帳失敗
:::

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|transaction_id|string|轉帳id|
|player_id|string| 玩家id|
|amount|decimal| 轉入額度|
|balance_before|decimal| 轉入前GameJar錢包餘額|
|balance_after|decimal| 轉入後GameJar錢包餘額|
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
    "data": {
        "transaction_id": "txn230329182045488",
        "player_id": "player0001",
        "amount": 999.99,
        "balance_before": 999.9900,
        "balance_after": 0.0000
    },
    "code": 0,
    "message": "success",
    "timestamp": 1681270032198
}
```
### 查詢轉帳結果

查詢 Deposit/Withdraw 結果
:::warning
若平台有失敗自動退款或重新查詢功能, 請交易失敗或逾時後重新查詢<br>
查詢務必取得 http status code = 200 結果方能參考<br>
回傳內容 code = 0 並且資料正確表示轉帳成功<br>
回傳內容 code = 206 表示轉帳失敗沒有該筆記錄
:::

:::danger
若返回 4xx 或 5xx 等錯誤請再重新查詢, 不可作資料異動
:::

**請求URL**
```
https://<url>/api/GetTransaction [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| transaction_id    | string     | 40    | Y | 轉帳id, 接受 A-Z, a-z, 0-9, - , _ |
```json!
{
  "transaction_id": "txn230329182045489"
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|transaction_id|string|轉帳id|
|player_id|string| 玩家id|
|amount|decimal| 轉入額度|
|balance_before|decimal| 轉入前GameJar錢包餘額|
|balance_after|decimal| 轉入後GameJar錢包餘額|
|type|int|交易類型: 1.Deposit 2.Withdraw |
|status|int|交易結果: 1.Waiting 2.Success 3.Fail 4.Pending |
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
  "data": {
    "transaction_id": "txn230329182045489",
    "player_id": "player001",
    "amount": 899.99,
    "balance_before": 100.99,
    "balance_after": 1000.98,
    "type": 1,
    "status": 2
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681278076366
}
```

### 取得遊戲列表

查詢遊戲列表

**請求URL**
```
https://<url>/api/GetGameList [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| game_platform    | string     | 10    | Y | [遊戲商](##附錄3.遊戲商) |
| page    | int     | Min:1 | Y | 第N頁, N從1開始 |
| count    | string     | Min: 100 Max: 10000    | Y | 每頁資料筆數|
```json!
{
  "game_platform": "JDB",
  "page": 1,
  "count": 100
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
|id|string|遊戲id, logingame請使用這個game_id|
|game_platform|string|[遊戲商](##附錄3.遊戲商)|
|game_name_mm|string| 遊戲名稱(緬)|
|game_name_en|string| 遊戲名稱(英)|
|game_name_ch|string| 遊戲名稱(中)|
|game_name_th|string| 遊戲名稱(泰)|
|game_name_vn|string| 遊戲名稱(越)|
|game_type|int|[遊戲類型](##附錄2.遊戲類型)|
|game_no|string|遊戲代碼|
|popular_game|bool|熱門 |
|enable_column|bool|是否開啟 |
|new_game|bool|新遊戲 |
|recommend_game|bool|推薦 |
| total_count     | int | 總筆數
| current_page     | int     | 目前頁數 | 
| total_page     | int     | 總頁數 | 
| count     | int     | 每頁筆數 | 
| timestamp     | long     | 回傳Unix時間戳記 | 
| code     | int | 參考錯誤代碼表
| message     | string     |  | 

```json!
{
  "data": {
    "game_list": [
         {
        "id": "104007003",
        "game_platform": "JDB",
        "game_name_mm": null,
        "game_name_en": "CaiShen Fishing",
        "game_name_ch": "财神捕鱼",
        "game_name_th": null,
        "game_name_vn": null,
        "game_type": 5,
        "game_no": "7003",
        "popular_game": false,
        "enable_column": true,
        "new_game": true,
        "recommend_game": false,
        "icon": "https://dl.lfyanwei.com/jdb-assetsv3/games/7003/7003_en.png",
        "create_datetime": "2023-04-12T14:20:40.02928+08:00",
        "update_datetime": "2023-04-12T14:20:40.0292816+08:00"
      },
      {
        "id": "104007007",
        "game_platform": "JDB",
        "game_name_mm": null,
        "game_name_en": "Fishing Disco",
        "game_name_ch": "捕鱼迪斯可",
        "game_name_th": null,
        "game_name_vn": null,
        "game_type": 5,
        "game_no": "7007",
        "popular_game": false,
        "enable_column": true,
        "new_game": true,
        "recommend_game": false,
        "icon": "https://dl.lfyanwei.com/jdb-assetsv3/games/7007/7007_en.png",
        "create_datetime": "2023-04-12T14:20:40.0293061+08:00",
        "update_datetime": "2023-04-12T14:20:40.0293062+08:00"
      }
    ],
    "total_count": 139,
    "current_page": 1,
    "total_page": 2,
    "count": 100
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681280409800
}
```
### 登入遊戲

登入遊戲取得遊戲URL


**請求URL**
```
https://<url>/api/LoginGame [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| player_id    | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _ |
| game_id     | string     | 40     | Y | 轉帳id唯一值, 接受 A-Z, a-z, 0-9, - , _ |
| device     | int     | 1-2 | Y | 裝置類型 1.網頁版 2.手機板 |
| language     | string     | 10 | N | 參考[語系代碼表](##附錄4.語系代碼)
| return_url     | string     | 255 | N | 關閉遊戲跳轉網址
| limit_gruop     | string     | 10 | N | 下注限紅組別
| odds_gruop     | string     | 10 | N | 盤口組別

```json!
{
  "player_id": "player0001",
  "transaction_id": "txn230329182045488",
  "amount": 999.99
}
```

### 登出遊戲

將玩家從遊戲中踢出


**請求URL**
```
https://<url>/api/LogoutGame [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| player_id    | string     | 3-20     | Y | 接受 A-Z, a-z, 0-9, - , _ |

```json!
{
  "player_id": "player0001"
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
    "code": 0,
    "message": "success",
    "timestamp": 1681285559953
}
```
### 依最大單號取得遊戲記錄

請求時請帶入version_key, 若第一次請求帶入0即可<br>
GameJar會回傳大於帶入version_key的遊戲記錄最多10,000筆<br>
下一次請求請帶入已回傳的所有記錄最大version_key

:::warning
GetBetRecordsByVersionKey 只會回傳24小時內的資料<br> 
超過24小時的資料請用GetBetRecordsByTimePeriod取得
:::

:::info
建議每次請求API的頻率在20-60秒之間<br> 
若取得資料筆數是0 建議60秒後再請求
:::

**請求URL**
```
https://<url>/api/GetBetRecordsByVersionKey  [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| version_key    | long     || Y | 帶入已經取得的最大值|

```json!
{
  "version_key": 1681302420965
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| id     | string     | 遊戲商record id 唯一值 <br>若是要當作PK需與game_platform同時建立 | 
| version_key     | long | 記錄版號, 使用此值來取得較新記錄|
| game_id     | string | 遊戲唯一id|
| player_id     | string | player id|
| currency     | string | 貨幣代碼|
| bet_amount     | deciaml | 投注金額|
| valid_bet_amount     | deciaml | 實際扣款金額|
| turnover     | deciaml | 投注流水|
| win     | deciaml | 遊戲贏分|
| fee     | deciaml | 遊戲抽傭|
| reward     | deciaml | 遊戲額外獎勵|
| netwin     | deciaml | 遊戲淨贏 (win - valid_bet_amount - fee +  reward)|
| jackpot_contribute     | deciaml | 彩金貢獻值 (valid_bet_amount已經包含此值)|
| jackpot_win     | deciaml | 彩金贏分(win已經包含此值)|
| bet_time     | datetime | 投注時間(GMT+8)|
| settlement_time     | datetime | 結算時間(GMT+8)|
| freegame     | int | 是否為免費遊戲 0:一般遊戲 1:免費遊戲|
| status     | int | 結算狀態 0:未結算 1:已結算 2.取消|
| sub_agent_id     | string | 子代理|
| agent_id     | string | 代理|
| round_id     | string | 場次編號(例:多人遊戲的局號)|
| game_platform     | string |遊戲商id|
| game_type     | int | [遊戲類型](##附錄2.遊戲類型)|
| bet_type     | int | 投注區類型|
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)|
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
  "data": {
    "bet_records": [
      {
        "id": "5250176658410",
        "version_key": 1681302540984,
        "game_id": "104014060",
        "player_id": "jfpl001",
        "currency": "THB",
        "bet_amount": 0.5,
        "valid_bet_amount": 0.5,
        "turnover": 0.5,
        "win": 17.2,
        "netwin": 16.7,
        "fee": 0,
        "reward": 0,
        "jackpot_contribute": 0.0025,
        "jackpot_win": 0,
        "bet_time": "2023-04-12T20:23:06",
        "settlement_time": "2023-04-12T20:23:06",
        "freegame": 0,
        "status": 1,
        "sub_agent_id": "joyfunsa",
        "round_id": null,
        "agent_id": "h12",
        "game_platform": "JDB",
        "game_type": 4,
        "bet_type": 0
      },
      {
        "id": "5250176658411",
        "version_key": 1681302540984,
        "game_id": "104014060",
        "player_id": "jfpl001",
        "currency": "THB",
        "bet_amount": 0.5,
        "valid_bet_amount": 0.5,
        "turnover": 0.5,
        "win": 0,
        "netwin": -0.5,
        "fee": 0,
        "reward": 0,
        "jackpot_contribute": 0.0025,
        "jackpot_win": 0,
        "bet_time": "2023-04-12T20:23:22",
        "settlement_time": "2023-04-12T20:23:22",
        "freegame": 0,
        "status": 1,
        "sub_agent_id": "joyfunsa",
        "round_id": null,
        "agent_id": "h12",
        "game_platform": "JDB",
        "game_type": 4,
        "bet_type": 0
      }
    ]
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681368281718
}
```

### 依時間範圍取得遊戲記錄

:::warning
最久可以取得60天前資料<br> 
一次取得最大的時間範圍為5分鐘
:::

:::info
時間最小單位為秒<br>
建議每次請求API的頻率在20-60秒之間<br> 
請求時間小於現在時間-10分鐘以上才可以減少遺漏記錄<br>
回傳 start_time <= settlement_time <end_time 記錄
:::

**請求URL**
```
https://<url>/api/GetBetRecordsByTimePeriod  [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| start_time    | DateTime     || Y | ISO 8601 <br>2023-04-12T12:23:00Z <br> 2023-04-12T20:23:00+08:00<br>二個時間相同皆可使用|
| end_time    | DateTime     || Y | ISO 8601|
| page    | int     | Min:1 | Y | 第N頁, N從1開始 |
| count    | string     | Min: 100 Max: 10000    | Y | 每頁資料筆數|

```json!
{
  "start_time": "2023-04-12T12:23:00Z",
  "end_time": "2023-04-12T12:24:00Z",
  "page": 1,
  "count": 1000
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| id     | string     | 遊戲商record id 唯一值 <br>若是要當作PK需與game_platform同時建立 | 
| version_key     | long | 記錄版號, 使用此值來取得較新記錄|
| game_id     | string | 遊戲唯一id|
| player_id     | string | player id|
| currency     | string | 貨幣代碼|
| bet_amount     | deciaml | 投注金額|
| valid_bet_amount     | deciaml | 實際扣款金額|
| turnover     | deciaml | 投注流水|
| win     | deciaml | 遊戲贏分|
| fee     | deciaml | 遊戲抽傭|
| reward     | deciaml | 遊戲額外獎勵|
| netwin     | deciaml | 遊戲淨贏 (win - valid_bet_amount - fee +  reward)|
| jackpot_contribute     | deciaml | 彩金貢獻值 (valid_bet_amount已經包含此值)|
| jackpot_win     | deciaml | 彩金贏分(win已經包含此值)|
| bet_time     | datetime | 投注時間(GMT+8)|
| settlement_time     | datetime | 結算時間(GMT+8)|
| freegame     | int | 是否為免費遊戲 0:一般遊戲 1:免費遊戲|
| status     | int | 結算狀態 0:未結算 1:已結算 2.取消|
| sub_agent_id     | string | 子代理|
| agent_id     | string | 代理|
| round_id     | string | 場次編號(例:多人遊戲的局號)|
| game_platform     | string |遊戲商id|
| game_type     | int | [遊戲類型](##附錄2.遊戲類型)|
| bet_type     | int | 投注區類型|
| total_count     | int | 總筆數
| current_page     | int     | 目前頁數 | 
| total_page     | int     | 總頁數 | 
| count     | int     | 每頁筆數 | 
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)|
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
  "data": {
    "bet_records": [
      {
        "id": "5250176658418",
        "version_key": 1681302540984,
        "game_id": "104014060",
        "player_id": "jfpl001",
        "currency": "THB",
        "bet_amount": 0.5,
        "valid_bet_amount": 0.5,
        "turnover": 0.5,
        "win": 0,
        "netwin": -0.5,
        "fee": 0,
        "reward": 0,
        "jackpot_contribute": 0.0025,
        "jackpot_win": 0,
        "bet_time": "2023-04-12T20:23:32",
        "settlement_time": "2023-04-12T20:23:32",
        "freegame": 0,
        "status": 1,
        "sub_agent_id": "joyfunsa",
        "round_id": null,
        "agent_id": "h12",
        "game_platform": "JDB",
        "game_type": 4,
        "bet_type": 0
      },
      {
        "id": "5250176658417",
        "version_key": 1681302540984,
        "game_id": "104014060",
        "player_id": "jfpl001",
        "currency": "THB",
        "bet_amount": 0.5,
        "valid_bet_amount": 0.5,
        "turnover": 0.5,
        "win": 0,
        "netwin": -0.5,
        "fee": 0,
        "reward": 0,
        "jackpot_contribute": 0.0025,
        "jackpot_win": 0,
        "bet_time": "2023-04-12T20:23:31",
        "settlement_time": "2023-04-12T20:23:31",
        "freegame": 0,
        "status": 1,
        "sub_agent_id": "joyfunsa",
        "round_id": null,
        "agent_id": "h12",
        "game_platform": "JDB",
        "game_type": 4,
        "bet_type": 0
      }      
    ],
    "total_count": 2,
    "current_page": 1,
    "total_page": 1,
    "count": 1000
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681372551113
}

```
### 取得遊戲詳細畫面連結

取得玩家單場遊戲詳細結果畫面


**請求URL**
```
https://<url>/api/GetBetRecordDetail [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| bet_record_id    | string     | 50     | Y |  |
| game_id    | string     | 10     | Y | 遊戲唯一id |
| language    | string     | 10     | N | 參考[語系代碼表](##附錄4.語系代碼) 預設:en-US|

```json!
{
  "bet_record_id": "5250176662334",
  "game_id": "104015009",
  "language": "zh-CN"
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| url     | string     | 遊戲記錄畫面連結 | 
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 
```json!
{
  "data": {
    "url": "https://player.jdb711.com?e=api_demo&..."
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681452577668
}
```
## 取得每小時遊戲統計報表

取得小時統計報表

**請求URL**
```
https://<url>/api/GetHourlyReport [POST]
```

**請求內容 Request body**

| 參數名稱 | 型別 | 長度限制 | 必填 | 說明|
| -------- | -------- | -------- |-------- |-------- |
| start_time    | DateTime     || Y | ISO 8601|
| end_time    | DateTime     || Y | ISO 8601|
| report_type    | int     |      | Y | 報表統計類型 1.代理 2.子代理 3.玩家 4.遊戲商 5.遊戲類型|

```json!
{
  "start_time": "2023-04-14T03:00:00Z",
  "end_time": "2023-04-14T04:00:00Z",
  "report_type": 1
}
```

**回傳內容 Response body**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| reports     | array     | 參考reports資料 | 
| code     | int | 參考[錯誤代碼表](##附錄1.錯誤代碼表)
| message     | string     |  | 
| timestamp     | long     | 回傳Unix時間戳記 | 

**回傳內容 reports**

| 參數名稱 | 型別 | 說明|
| -------- | -------- | -------- |
| report_time     | array     | 統計時間 <br>2023-04-14T11:00:00+08:00表示統計 <br>2023-04-14 11:00:00 到 2023-04-14 11:59:59| 
| bet_amount     | deciaml | 投注金額|
| valid_bet_amount     | deciaml | 實際扣款金額|
| turnover     | deciaml | 投注流水|
| win     | deciaml | 遊戲贏分|
| fee     | deciaml | 遊戲抽傭|
| reward     | deciaml | 遊戲額外獎勵|
| netwin     | deciaml | 遊戲淨贏 (win - valid_bet_amount - fee +  reward)|
| jackpot_contribute     | deciaml | 彩金貢獻值 (valid_bet_amount已經包含此值)|
| jackpot_win     | deciaml | 彩金贏分(win已經包含此值)|
| rows_count     | int | 統計筆數|
```json!
{
  "data": {
    "reports": [
      {
        "report_time": "2023-04-14T11:00:00+08:00",
        "bet_amount": 24.4,
        "valid_bet_amount": 24.4,
        "turnover": 24.4,
        "win": 50.05,
        "netwin": 25.65,
        "fee": 0,
        "reward": 0,
        "jackpot_contribute": 0.1232,
        "jackpot_win": 0,
        "rows_count": 74
      }
    ]
  },
  "code": 0,
  "message": "success",
  "timestamp": 1681454208546
}
```

## 附錄1.錯誤代碼表

| 代碼 | 訊息 | 說明|
| -------- | -------- | -------- |
| 0     | success     |      |
| 101   | player not exist     |   使用者未建立成功 |
| 201   | create game player fail     |    遊戲商使用者建立失敗  |
| 202   | game maintain     |    遊戲維護中  |
| 203   | game api unstable (time out more than 10 times)  | 目前遊戲API不穩定請稍後再試     |
| 204   | unavailable game platform  |  不允許的遊戲商    |
| 205   | game id not exist  |   遊戲ID錯誤   |
| 206   | bet record id not exist  |   遊戲記錄ID錯誤   |
| 301   | transaction id duplicated  |   重複的交易參考id   |
| 302   | insufficient credit  |   額度不足, 若是遊戲進行中請先登出player   |
| 303   | transaction id not exist  |   沒有該轉帳記錄   |
| 901   | request parameter wrong  |  請求參數有誤, 請再確認請求參數內容    |
| 902   | currency incorrect  |  不支援的貨幣   |
| 904   | access denied  |   拒絕存取   |
| 907   | api requests are too frequent  |  請求頻率太高, 請參考API請求限制    |
| 998   | unknow fail  |      |
| 999   | request time out  |  可能系統忙碌中或網路連線不穩定 |

## 附錄2.遊戲類型

| 代碼 | 類型 | 說明|
| -------- | -------- | -------- |
|0|真人賭場遊戲 |視訊真人(機械)賭桌遊戲|
|1|電子賭場遊戲 |電子RNG模擬荷官|
|2|體育 |真人賽事|
|3|虛擬體育 |電子模擬賽事|
|4|老虎機 ||
|5|捕魚機 ||
|6|街機 |非老虎機與捕魚機的電子遊戲|
|7|賓果 ||
|8|彩票 ||
|9|棋牌 ||


## 附錄3.遊戲商

| 遊戲商 | 說明 | 
| -------- | -------- | 
| JDB     |  奪寶電子    | 
| PG     |  Pocket Gaming    | 
| JILI     |  吉利電子    | 
| JOCKER     |      |
| DS     |  Draggon Soft    | 
| AMEBA     |  阿米巴電子    | 
| SABA   |  沙巴體育    | 

## 附錄4.語系代碼

| 代碼 | 說明 | 
| -------- | -------- | 
| en-US    |  英文    | 
| zh-TW    |  繁體中文    | 
| zh-CN    |  簡體中文    | 
| th-TH    |  泰文    |
| ko-KR    |  韓文 | 
| en-MY    |  緬甸文    | 
| vi-VN    |  越南文    | 
| ja-JP    |  日文    | 
| id-ID    |  印尼文    | 