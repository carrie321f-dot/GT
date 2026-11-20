# TaskPlus：加強版 Google Tasks

單一檔案（`index.html`）網頁 app，直接同你嘅 Google Tasks 雙向同步，冇第三方 server。

## 功能

- **分類**：對應 Google Tasks 嘅「清單」，新增 / 改名 / 刪除都會同步
- **標籤**：快速新增時打 `#標籤`，側邊欄可按標籤篩選
- **優先度**：快速新增時打 `!1`–`!3`（低 / 中 / 高），checkbox 顏色會跟
- **多層子任務**：Google Tasks 只有一層，呢度可以無限層，可摺疊，顯示完成進度（2/5）
- **搜尋**：即時搜尋標題、備註、標籤（按 `/` 聚焦）
- **視圖**：全部、今日 / 逾期、即將到期、按分類、按標籤；可按到期日 / 優先度排序
- **編輯**：改標題、備註、到期日、優先度、標籤、移去另一個分類、改上層任務
- 有到期日嘅任務會自動出現喺 Google 日曆（Google 只支援日期，唔支援時間）

## 設定（只需一次）

1. 去 https://console.cloud.google.com 開新 Project
2. 「API 和服務」→「程式庫」→ 啟用 **Google Tasks API**
3. 「OAuth 同意畫面」→ External，保持 Testing 狀態，喺 **Test users** 加你自己個 Gmail
4. 「憑證」→ 建立憑證 → OAuth 用戶端 ID → **網頁應用程式**
5. 「已授權的 JavaScript 來源」加 `http://localhost:8000`（或你部署嘅網址）
6. 複製 Client ID

## 執行

```
cd 呢個資料夾
python3 -m http.server 8000
```

開 http://localhost:8000 ，貼上 Client ID，用 Google 登入。
（唔可以直接雙擊開 `index.html`，Google 登入要 http(s) 網址。）
你亦可以放上 GitHub Pages / Netlify，記得將佢個網址加入「已授權的 JavaScript 來源」。

## 安裝去手機（PWA）

PWA 要 **https** 網址先裝得到，手機連唔到你電腦嘅 localhost，所以要先部署（免費）：

1. 將成個資料夾（`index.html`、`manifest.json`、`sw.js`、`icons/`）放上 GitHub Pages / Netlify / Cloudflare Pages 等靜態空間
2. 將部署後嘅網址（例如 `https://你的名.github.io`）加入 OAuth Client ID 嘅「已授權的 JavaScript 來源」（只要網域，唔使加路徑）
3. 手機瀏覽器開嗰個網址並登入
   - **Android Chrome**：選單 →「安裝應用程式」／「加至主畫面」
   - **iPhone Safari**：分享 →「加入主畫面」
4. 之後由主畫面圖示開，就似原生 app 咁全螢幕

離線時會顯示上次同步嘅資料，但唔可以改；有網絡後會自動同步。
更新 app 後如果手機冇即時變，閂咗再開一次。

## 資料點存

Google Tasks 冇標籤同優先度欄位，所以 TaskPlus 將呢啲資料放喺任務**備註最尾**，格式係：

```
[[tt:{"tags":["工作"],"pri":2}]]
```

喺 Google Tasks / 日曆入面你會見到呢行字，唔好刪走佢，否則標籤會冇咗。
深於一層嘅子任務，喺 Google 本身會顯示成同一層，只有 TaskPlus 會顯示完整層級。

## 限制

- 唔支援離線編輯（有網絡先可以改）
- 同步方式係「後改嘅贏」，每 90 秒及切換返分頁時會自動拉取最新資料
- 登入 token 約 1 小時過期，過期後會自動靜默更新，失敗就要再按登入
- Testing 狀態嘅 OAuth app 登入時會見到「未經驗證」警告，係正常嘅（只有你自己用）
