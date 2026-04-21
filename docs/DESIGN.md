# 多多研究報告部落格 - 設計文件

**版本：** 1.0
**日期：** 2026-04-21
**狀態：** 已批准
**BRANCH：** feature/jekyll-site

---

## 1. 目標

將多多的研究報告從 GitHub 原始 Markdown 檔案，升級成一個漂亮的、可以從任何設備訪問的部落格網站。

---

## 2. 技術方案

### 選擇：GitHub Pages + Jekyll

| 技術 | 理由 |
|------|------|
| GitHub Pages | 免費托管，已啟用 |
| Jekyll | GitHub 原生支援，自動把 Markdown 轉 HTML |
| Just the Docs 主題 | 專為文件庫設計，乾淨漂亮 |
| Ruby (via github-pages gem) | 本地預覽用 |

---

## 3. 目錄結構

```
duoduo-reports/
├── _config.yml              # Jekyll 設定
├── Gemfile                  # Ruby 依賴
├── index.md                 # Jekyll 首頁（來自現有 index.md）
├── _layouts/                # 頁面模板
├── _includes/               # 可重用元件
├── reports/                 # 研究報告 Markdown
│   ├── 2026-04-21-2355-JingPeng.md
│   └── ...
├── slides/                 # HTML 投影片
│   ├── 2026-04-21-2355-JingPeng.html
│   └── ...
└── docs/                   # 設計文件
    └── DESIGN.md
```

---

## 4. URL 結構

| 類型 | URL |
|------|-----|
| 首頁 | `https://laohuyazi2026-design.github.io/duoduo-reports/` |
| 報告列表 | `/duoduo-reports/reports/` |
| 單篇報告 | `/duoduo-reports/reports/{slug}/` |
| 投影片 | `/duoduo-reports/slides/{filename}.html` |

---

## 5. 第一階段交付物

### 5.1 已批准的功能

- [ ] Jekyll 設定完成（_config.yml, Gemfile）
- [ ] Just the Docs 主題設定
- [ ] index.md 轉成 Jekyll 首頁
- [ ] reports/ 內的 Markdown 自動轉 HTML
- [ ] slides/ 內的 HTML 投影片可訪問
- [ ] 手機響應式設計
- [ ] 本地預覽功能

### 5.2 不包含（第二階段）

- 搜尋功能
- 自動分類
- RSS 訂閱

---

## 6. 數據流向

```
多多寫報告（Markdown + front matter）
       ↓
放到 reports/ 資料夾
       ↓
git push
       ↓
GitHub Pages 自動 Jekyll 構建（原生支援，無需 GitHub Actions）
       ↓
發布到 GitHub Pages
       ↓
讀者訪問漂亮的部落格網站
```

**注意**：所有 Markdown 檔案開頭必須有 front matter（如下），否則 Jekyll 不會轉換：

```yaml
---
layout: default
title: 敬鵬研究報告
permalink: /reports/2026-04-21-jingpeng/
nav_order: 1
---
```

### index.md 的實作說明

現有的 `index.md` 是多多的研究報告總索引（包含所有報告的列表）。

轉換成 Jekyll 首頁需要的改動：
1. 開頭加入 front matter（如上）
2. 內容可以保留，只是 Jekyll 會自動套用 Just the Docs 的樣式

### 報告列表頁的實作

URL `/reports/` 的列表頁由 Just the Docs 的導航功能產生。

需要在每個報告的 front matter 加入：
```yaml
parent: reports
nav_order: 1
```

Just the Docs 會自動根據 `parent` 欄位產生側邊欄導航。

---

## 7. 主題選擇

**Just the Docs** (https://just-the-docs.com/)

選擇理由：
- 專為文件庫設計
- 內建導航、側邊欄
- 搜尋功能預留（第二階段啟用）
- 響應式設計優秀
- GitHub 官方推薦主題

---

## 8. 檔案命名規範

Markdown 報告檔案：`{YYYY-MM-DD}-{股票代碼}-{研究主題}.md`
例如：`2026-04-21-2355-JingPeng.md`

說明：
- `YYYY-MM-DD`：日期（如 2026-04-21）
- `股票代碼`：台股代碼（如 2355 是敬鵬的代碼）
- `研究主題`：研究的主題（如 JingPeng）

**注意**：此格式與 Jekyll Posts（`_posts/`）不同。本系統使用普通 `reports/` 資料夾，URL 由 Jekyll 預設行為決定，不使用 Jekyll Posts 功能。

Slug（URL）行為：
- Jekyll 預設會保持檔案名稱的大小寫
- 若要強制小寫，需在 front matter 加入 `permalink` 欄位
- 建議統一使用小寫 slug，避免大小寫問題

```yaml
---
layout: default
title: 敬鵬研究報告
permalink: /reports/2026-04-21-jingpeng/
---
```

---

## 9. 依賴與前提

- Ruby 3.0+（本地預覽用）
- github-pages gem
- GitHub Pages 設定：Build from branch `gh-pages`，folder `/ (root)`

### Branch 部署流程

```
feature/jekyll-site（開發分支）
       ↓
merge 到 main（經過測試後）
       ↓
main push 觸發 GitHub Actions
       ↓
GitHub Actions 自動同步到 gh-pages
       ↓
gh-pages 發布到 GitHub Pages
```

**注意**：gh-pages 由 GitHub Actions 自動維護，**請勿手動編輯 gh-pages**。

---

### slides/ 資料夾的 Jekyll 設定

slides/ 內的 HTML 投影片（如 Reveal.js）是**獨立格式**，不需要 Jekyll 處理。

在 `_config.yml` 中需明確排除：

```yaml
exclude:
  - slides/
```

或在每個投影片 HTML 開頭加入空的 front matter（讓 Jekyll 知道不要套 layout）：

```yaml
---
layout: null
---
```

---

### 本地預覽指令

```bash
# 安裝依賴
bundle install

# 啟動本地伺服器
bundle exec jekyll serve

# 開啟瀏覽器访问 http://localhost:4000
```

存檔後 Jekyll 會自動重建並刷新瀏覽器，無需重新執行指令。

---

## 10. 拒絕的方案

| 方案 | 理由 |
|------|------|
| 自建 HTML + CSS | 維護成本高，第一階段不需要 |
| Hugo | Jekyll 與 GitHub Pages 整合更緊密 |
| Next.js | 太複雜，過度設計 |

---

_設計批准：2026-04-21_