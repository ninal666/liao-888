# 🌏 WorldPulse 環球快報
### GitHub Actions 全自動每日新聞網頁

每天台灣時間早上 7:00 自動從 NewsAPI 抓取即時新聞，重新生成網頁，部署到 GitHub Pages。

---

## 🚀 快速開始（5步驟）

### 第一步：建立 GitHub 倉庫

1. 登入 [github.com](https://github.com)
2. 點右上角 **+** → **New repository**
3. Repository name 填：`worldpulse`（或任何名稱）
4. 選 **Public**（GitHub Pages 免費版需要公開）
5. 勾選 **Add a README file**
6. 點 **Create repository**

---

### 第二步：上傳專案檔案

把這個資料夾的所有檔案上傳到你的倉庫：

```
worldpulse-auto/
├── .github/
│   └── workflows/
│       └── daily-update.yml   ← GitHub Actions 排程設定
├── scripts/
│   └── fetch_and_build.py     ← 抓新聞 + 生成 HTML 的主程式
├── docs/
│   └── index.html             ← 生成的網頁（第一次可先空著）
└── README.md
```

**上傳方法（擇一）：**

**方法 A — 網頁直接上傳**
- 在倉庫頁面點 **Add file** → **Upload files**
- 把所有檔案拖進去，注意要保持資料夾結構

**方法 B — 用 Git 命令列**
```bash
git clone https://github.com/你的帳號/worldpulse.git
# 把所有檔案複製進去
git add .
git commit -m "初始化 WorldPulse 專案"
git push
```

---

### 第三步：申請 NewsAPI Key（免費）

1. 前往 [newsapi.org/register](https://newsapi.org/register)
2. 填寫姓名、Email、密碼
3. 驗證 Email 後登入
4. 首頁會顯示你的 **API Key**（一串英數字）
5. 複製保存

> ✅ 免費方案每日 100 次請求，足夠每天自動更新使用

---

### 第四步：設定 GitHub Secret（保護 API Key）

1. 進入你的 GitHub 倉庫
2. 點上方 **Settings** → 左側 **Secrets and variables** → **Actions**
3. 點 **New repository secret**
4. Name 填：`NEWS_API_KEY`
5. Secret 貼上剛才複製的 API Key
6. 點 **Add secret**

---

### 第五步：啟用 GitHub Pages

1. 倉庫 **Settings** → 左側 **Pages**
2. Source 選 **Deploy from a branch**
3. Branch 選 **gh-pages**，資料夾選 **/ (root)**
4. 點 **Save**

> 💡 `gh-pages` 分支會在第一次執行 Actions 後自動建立

---

## ▶️ 手動觸發第一次更新

1. 進入倉庫 → 點上方 **Actions** 標籤
2. 左側點 **每日新聞自動更新**
3. 右側點 **Run workflow** → **Run workflow**
4. 等待約 1-2 分鐘執行完成（綠色勾勾表示成功）
5. 前往 `https://你的帳號.github.io/worldpulse` 查看網頁 🎉

---

## ⏰ 自動更新時程

| 說明 | 時間 |
|------|------|
| 台灣時間 | 每天早上 07:00 |
| UTC 時間 | 每天 23:00 |
| Cron 設定 | `0 23 * * *` |

要修改時間，編輯 `.github/workflows/daily-update.yml` 中的 cron 值：
```yaml
# 台灣時間 = UTC + 8
# 台灣早上 9:00 = UTC 01:00 → cron: '0 1 * * *'
# 台灣下午 6:00 = UTC 10:00 → cron: '0 10 * * *'
```

---

## 📁 檔案說明

```
.github/workflows/daily-update.yml
  → GitHub Actions 排程工作流程
  → 每天自動執行 Python 腳本並部署

scripts/fetch_and_build.py
  → 核心程式：
    1. 呼叫 NewsAPI 抓取各地區新聞
    2. 清洗整理資料（去重、截斷、填補圖片）
    3. 注入 HTML 模板
    4. 輸出 docs/index.html 和 docs/news_data.json

docs/index.html
  → 生成的網頁（由腳本自動覆蓋，請勿手動編輯）

docs/news_data.json
  → 生成的新聞 JSON 資料（可直接查看原始資料）
```

---

## 🔧 自訂設定

### 修改抓取的新聞地區
編輯 `scripts/fetch_and_build.py` 中的 `REGION_QUERIES`：

```python
REGION_QUERIES = [
    {"id": "asia", "label": "亞太", "q": "Asia OR Taiwan OR Japan"},
    # 加入你想要的地區查詢關鍵字
]
```

### 修改每天抓取的新聞數量
```python
breaking = fetch_top("general", "us", 8)  # 改這個數字
```

### 加入第二次每日更新（下午）
在 `daily-update.yml` 的 schedule 加一行：
```yaml
schedule:
  - cron: '0 23 * * *'  # 台灣早上 7:00
  - cron: '0 10 * * *'  # 台灣下午 6:00
```

---

## ❓ 常見問題

**Q: Actions 執行失敗怎麼辦？**
A: 點進失敗的 workflow，查看紅色步驟的錯誤訊息。最常見原因是 `NEWS_API_KEY` 沒設定。

**Q: 網頁顯示示範資料而非真實新聞？**
A: 確認 Secret 名稱是否為 `NEWS_API_KEY`（區分大小寫），並重新手動觸發一次。

**Q: 免費 API 有限制嗎？**
A: NewsAPI 免費版每日 100 次請求、只能查最近一個月的新聞，對每日更新完全足夠。

**Q: 可以改成中文新聞嗎？**
A: 在 `fetch_region` 的 `lang` 參數改為 `"zh"` 試試，但 NewsAPI 中文新聞來源較少。

---

## 🌐 你的網站網址

部署成功後，網站將在：
```
https://你的GitHub帳號.github.io/worldpulse/
```
