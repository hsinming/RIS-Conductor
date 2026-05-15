# RIS Conductor 使用者指南

## 這是什麼？

RIS Conductor 是一款自動化放射科報告工具。它能自動從 RIS（放射資訊系統）讀取病人檢查清單、從 PACS（影像存檔系統）擷取影像、呼叫 AI 產生報告草稿，並將結果貼回 RIS 報告編輯器。

簡單來說：**選好病人清單 → 按下開始 → AI 自動產出報告草稿**。

## 系統需求

- **Windows 10 或 Windows 11**
- **WebView2 Runtime**（Windows 11 已內建；Windows 10 通常也已安裝）
- **AutoHotkey v2**（僅原始碼版本需要；.exe 版本免安裝）

## 安裝方式

### 方式一：使用 .exe 版本（推薦）

1. 從 GitHub Releases 下載最新版的 `RIS-Conductor.exe`
2. 將 exe 檔放在任意資料夾中
3. 雙擊執行即可

### 方式二：使用原始碼版本

1. 安裝 [AutoHotkey v2](https://www.autohotkey.com/)
2. 下載或複製本專案原始碼
3. 雙擊 `main.ahk` 或執行 `.\_run_main.ps1`

## 首次設定

第一次啟動時，會顯示設定畫面，請依序填寫：

### 1. 選擇 RIS 系統

| 選項 | 適用醫院 |
|---|---|
| NTUH KReport | 台大醫院 |
| TAH PowerBuilder | 臺安醫院 |

### 2. 選擇 PACS 來源

| 選項 | 說明 |
|---|---|
| NTUH PACS (HTTP API) | 台大醫院 — 透過 API 擷取影像（推薦） |
| NTUH PACS (Screenshot) | 台大醫院 — 透過螢幕擷取 |
| NTUCC / HCH / BIH / YLH PACS (HTTP API) | 台大各分院 |
| TAH PACS (Screenshot) | 臺安醫院 — 螢幕擷取 |

> 若選擇 HTTP API 方式，後續需要輸入 PACS 帳號密碼。

### 3. 選擇 AI 服務商與模型

| 服務商 | 說明 |
|---|---|
| OpenRouter | 整合多家 AI 模型的閘道器（推薦） |
| OpenAI | 直接使用 OpenAI API |
| Google Gemini | 直接使用 Google Gemini API |
| Anthropic | 直接使用 Anthropic Claude API |
| HuggingFace | 使用 HuggingFace 端點 |

選擇服務商後，會自動列出可用的模型清單，也可手動輸入模型名稱。

### 4. 輸入 API Key

將您從 AI 服務商取得的 API Key 貼入欄位。此金鑰僅儲存於本機 `settings.ini` 中，不會傳送至任何第三方。

### 5. 儲存並繼續

填寫完畢後按下「Save & Continue」即可進入主畫面。

## 如何申請 API Key

### OpenRouter（推薦）

1. 前往 [https://openrouter.ai/](https://openrouter.ai/)
2. 註冊帳號
3. 進入 **Keys** 頁面
4. 點擊 **Create Key**，複製產生的金鑰（格式：`sk-or-v1-...`）

### OpenAI

1. 前往 [https://platform.openai.com/](https://platform.openai.com/)
2. 登入或註冊帳號
3. 進入 **API keys** 頁面
4. 點擊 **Create new secret key**，複製金鑰（格式：`sk-proj-...`）

### Google Gemini

1. 前往 [https://aistudio.google.com/](https://aistudio.google.com/)
2. 使用 Google 帳號登入
3. 點擊 **Get API key**，建立並複製金鑰

### Anthropic

1. 前往 [https://console.anthropic.com/](https://console.anthropic.com/)
2. 註冊或登入帳號
3. 進入 **API Keys** 頁面
4. 建立並複製金鑰（格式：`sk-ant-...`）

### HuggingFace

1. 前往 [https://huggingface.co/](https://huggingface.co/)
2. 登入帳號
3. 進入 **Settings → Access Tokens**
4. 建立 Token（格式：`hf_xxxxxxxxxxxxx`）
5. 若使用 Inference Endpoint，還需準備端點 URL

## 使用方式

### 批次處理流程

1. **載入清單**：點擊「Load Worklist」按鈕，系統會從 RIS 讀取待處理的病人清單
2. **確認清單**：下方表格會顯示所有待處理病人（病歷號、檢查號、姓名、檢查項目）
3. **開始處理**：點擊「Start」按鈕，系統會依序處理每位病人：
   - 從 PACS 擷取影像
   - 傳送給 AI 產生報告
   - 將報告草稿貼回 RIS
4. **查看進度**：進度條即時顯示處理進度，日誌區顯示每筆病人的處理狀態
5. **完成**：處理完畢後會顯示失敗清單（如有）

### 報告草稿檢視器

點擊「Open Draft Viewer」可開啟獨立的草稿檢視視窗，功能包括：

- 瀏覽所有已產生的報告草稿
- 檢視、編輯報告內容
- **Paste to RIS**：將草稿貼回 RIS 報告編輯器
- 複製報告內容至剪貼簿
- 批次刪除草稿

### 修改設定

點擊右上角 ⚙ 圖示可開啟設定視窗，隨時可修改：

- RIS / PACS 系統切換
- AI 服務商、模型、API Key 更換
- 重試次數、超時時間
- 貼入模式（覆蓋 / 附加 / 插入 / 跳過 / 保留）
- 強制重新產生（忽略已有草稿）

### 貼入模式說明

| 模式 | 行為 |
|---|---|
| 覆蓋 (Overwrite) | 取代 RIS 報告欄位原有內容（預設） |
| 附加 (Append) | 將報告加在原有內容後方 |
| 插入 (Prepend) | 將報告加在原有內容前方 |
| 保留 (Keep) | 若 RIS 欄位已有內容則跳過，不貼入 |
| 跳過 (Skip) | 不貼入 RIS |

## 常見問題

### Q: 為什麼有些病人處理失敗？

可能原因：
- PACS 中找不到該病人的影像
- AI 服務超時或回傳錯誤
- RIS 視窗被其他程式遮擋
- 該病人的檢查項目沒有對應的 AI 技能

失敗的病人會顯示在完成清單中，可手動重試。

### Q: 可以中途停止嗎？

可以。點擊「Stop」按鈕即可停止批次處理。

### Q: API Key 安全嗎？

API Key 僅儲存於本機的 `settings.ini` 設定檔中，不會上傳至任何伺服器。

### Q: 支援哪些檢查項目？

目前支援：胸部 X 光、脊椎 X 光、腹部 X 光、骨骼 X 光、顱骨 X 光、面部 X 光、骨齡 X 光、頸部 X 光，以及其他未分類的影像檢查（通用模式）。

### Q: 需要網路連線嗎？

需要。AI 報告產生必須連線至 AI 服務商的 API。PACS 若使用 HTTP API 方式也需要網路。