# DevApp_md｜AI 智慧計畫書管理（A 階段）精簡計畫書 v1.0

> 規劃日期：2026-11-01  
> GitHub：https://github.com/ilifeup/DevApp_md  
> 狀態：規劃草案（尚未核准成為正式執行規範）  
> 範圍：A｜ChatGPT Plus／Claude Pro → GitHub 工具或 API → GitHub Markdown  
> 不包含：B 階段 PHP Cron、MySQL、Web；不建置自訂 MCP Server、AI Agent 或 GitHub Actions。

## 1. 目標與原則

將多份 App 開發文件整理為 AI 易讀、易比對、可持續迭代、具決議約束的正式 Markdown 計畫書。

1. **只用一個 AGENTS.md 管所有 AI**：ChatGPT、Claude 及其他 AI 都以此為共同規則；不建立 CLAUDE.md。不同工具未必自動讀取，執行時需明確指示。
2. **一模組一份正式規格**：`modules/<MODULE-ID>.md` 為唯一正式來源，其他文件僅引用，不複製完整規格。
3. **三版本軸分開**：需求規格、程式實作、測試驗收各自記錄版本、狀態與證據。文件更新不等於程式完成，CI 綠燈不等於驗收通過。
4. **新資料不直接覆蓋正式決議**：AI 先比對差異，再依權限自動更新或送審。
5. **少檔案、少重複、少維護**：優先使用 GitHub Markdown、Commit 與 Diff。
6. **計畫與程式分離**：本儲存庫只存計畫及程式證據連結，不搬入 Flutter／Laravel 原始碼。

## 2. 精簡文件結構：六類職責

```text
DevApp_md/
├── AGENTS.md                 # AI 共用規範
├── README.md                 # 入口、索引與使用方式
├── PROJECT.md                # 專案目標、MVP、架構、路線圖
├── modules/
│   ├── MEDIA-01.md           # 唯一正式模組需求規格
│   └── ...
├── changes/
│   └── CHG-YYYYMMDD-001.md   # 變更提案、核准與差異
└── tracking/
    ├── MEDIA-01.md           # 三版本與證據
    └── ...
```

六類職責：AI 規範、文件入口、專案總覽、正式模組規格、變更紀錄、三版本追蹤。README 只做索引；PROJECT 合併原本的 PROJECT／ARCHITECTURE／ROADMAP／MODULES；tracking 合併 implementation／verification／feedback。

**不另建** CLAUDE.md、ARCHITECTURE.md、ROADMAP.md、MODULES.md、CHANGELOG.md、docs/decisions/、docs/contracts/、docs/feedback/ 等重複檔案。

首次僅建立 AGENTS.md、README.md、PROJECT.md、modules/MEDIA-01.md、tracking/MEDIA-01.md。有變更才建立 changes/CHG-*.md，不建立空白模組文件。

## 3. 各文件規範

### 3.1 AGENTS.md：唯一 AI 規範

- 讀取順序：AGENTS.md → README.md → PROJECT.md → 目標 modules/ → 對應 tracking/ → 相關 changes/。
- 明確規範正式規格來源、模組 ID、需求 ID、Markdown front matter。
- 未核准不得新增／刪除功能、API、資料表、權限、重要業務規則；不得推翻正式決議。
- **C 模式**：有證據的一般進度、非語意修改可自動處理；重大變更需使用者核准。
- 修改前須比對，輸出 AUTO_APPLY／REVIEW_REQUIRED／CONFLICT／NO_CHANGE。
- AI 宣稱完成不等於有實作證據；測試案例存在不等於測試通過。
- 只修改受影響文件，集中 Commit；不得擅自合併 PR 或操作正式資料庫。
- AGENTS.md 不保證所有 AI 工具自動識別，任務指令須要求先讀。

### 3.2 README.md：入口與索引

記錄專案用途、A 階段操作流程、專案清單、模組索引、待核准變更及新 MD 交付方式，不複製正式規格。

### 3.3 PROJECT.md：合併式總計畫

固定章節：
1. 專案定位與目標。
2. 使用者、MVP 範圍及排除事項。
3. 技術堆疊與已核准架構。
4. 模組總表與相依關係（連結 modules/）。
5. 開發里程碑、優先順序、阻礙。
6. 跨模組共用原則（細節以正式模組規格為準）。
7. 待決策事項與參考連結。

### 3.4 modules/<ID>.md：唯一正式需求規格

建議最小 front matter：

```yaml
---
module_id: MEDIA-01
title: 影音中心
spec_version: 1.0
spec_status: approved
approved_change: CHG-20261031-001
updated_at: 2026-11-01
---
```

章節：目的／範圍、角色權限、功能需求（固定 REQ-ID）、核心流程、UI 與 API／資料契約、驗收條件、明確排除功能、核准變更引用。

MEDIA-01 已知方向：音訊先下載完整 AES-256-GCM 加密檔再本機播放；管理端上傳上限 18 MiB；不得擅自加入伺服器端解密串流或線上直接播放。實際正式規格仍須核對已核准來源，不以本計畫書代替完整模組規格。

### 3.5 changes/CHG-*.md：一次變更一份紀錄

- 來源檔名、日期、網址或 Commit SHA，及影響模組。
- 逐項差異：新增／修改／刪除／重複／衝突。
- 需求 ID、舊規則、新提案、原因及風險。
- 狀態：proposed／approved／rejected／superseded。
- 核准人、日期、依據（如適用）。
- 更新結果：受影響正式檔案與 Git Commit。

**未核准提案不得寫入正式模組規格。** 已核准的正式規則只寫入 modules/；changes/ 保留歷史與差異，不維護第二份完整規格。

### 3.6 tracking/<ID>.md：三版本與 Feedback

| 版本軸 | 記錄內容 | 證據 |
|---|---|---|
| A 需求規格版 | spec_version、核准狀態、規格 Commit | modules/<ID>.md、核准變更 |
| B 程式實作版 | implementation_version、狀態、程式 Repo／Commit、覆蓋需求 ID | 程式變更、審查紀錄 |
| C 測試驗收版 | verification_version、狀態、測試時間、案例、結果 | 測試輸出、實機驗證、人工驗收 |

另設 Feedback／待處理差異：例如需求已核准但無程式證據、已有 Commit 但尚未驗收、程式宣稱支援被禁止功能。Feedback 只能提醒，不能自行修改正式需求。

三個版本號可不同；版本與狀態分開；沒有證據時填「待確認／未驗證」，不可猜測。

## 4. AI 更新標準流程（A 階段）

```text
使用者交付新 MD／開發報告／Commit 連結
                ↓
AI 讀 AGENTS.md、PROJECT.md、目標模組及 tracking
                ↓
辨識模組 ID／需求 ID／來源版本，比對正式規格
                ↓
產生差異分類及 changes/CHG-*.md
                ↓
一般進度／非語意修改 → 有來源證據可更新
重大業務規則／API／DB／權限變更 → 使用者先核准
                ↓
只更新受影響 modules/、tracking/、PROJECT.md
                ↓
檢查連結、需求 ID、版本與核准決議
                ↓
集中一次 Commit，回報 SHA、修改清單與待處理問題
```

無法確認來源、版本、核准狀態時，不修改正式規格，標示 CONFLICT 或 REVIEW_REQUIRED。沒有 GitHub 寫入權限時，只提供待提交內容，不得宣稱已提交。

## 5. AI 更新權限（C 模式）

| 變更 | AI 自動更新 |
|---|---|
| 有可靠來源的開發進度、Commit、測試證據 | 可以，寫 tracking/，不可冒充驗收 |
| 索引、錯字、連結等非語意修正 | 可以 |
| 新增或改變功能 | 不可以，先核准 |
| 刪除功能、API、資料表、修改權限 | 不可以，先核准 |
| 變更架構、重要流程、正式決議 | 不可以，先核准 |
| 修改 AGENTS.md 核心規則 | 不可以，先核准 |
| 自動合併 PR、正式部署、操作正式 DB | 不可以 |

## 6. AI 工具與 GitHub 使用

- **ChatGPT Plus**：整理需求、比對、審查、提出差異，具備授權時更新 GitHub；若無寫入權限，交付可提交 Markdown。
- **Claude Pro**：依相同 AGENTS.md 分析、整理開發報告、更新 GitHub；實際功能依所連接工具權限。
- **GitHub**：唯一正式 Markdown 儲存來源；Commit／Diff 保留歷史。重大變更可使用分支／PR 審核，不要求 GitHub Actions。
- **跨 AI 交接**：提供 Repo URL、模組 ID、新來源 MD，並要求先讀 AGENTS.md。

通用任務指令：

> 請先讀取 https://github.com/ilifeup/DevApp_md 的 AGENTS.md、README.md、PROJECT.md，再讀取目標 modules/<ID>.md、tracking/<ID>.md 及相關 changes/。分析新 MD，依需求 ID 逐項比對並回報新增、修改、刪除、衝突及證據。一般有證據的進度可更新；涉及正式規格、API、資料庫、權限或重要決議先等我核准。不得增加未核准功能；完成後回報實際修改檔案與 Commit SHA。無寫入權限時不得宣稱已提交。

## 7. 實施階段與驗收

### P0｜最小骨架

建立 AGENTS.md、README.md、PROJECT.md、modules/MEDIA-01.md、tracking/MEDIA-01.md。先驗證一個模組，不一次產生 45 個空白文件。

### P1｜MEDIA-01 試跑

1. 依正式核准來源建立 MEDIA-01 規格及需求 ID。
2. 輸入新 Claude MD，包含進度與可能衝突的建議。
3. AI 產出 changes/CHG-*.md，正確辨識禁止線上串流等衝突。
4. 未核准不得更動正式規格；可獨立更新有證據的實作／測試狀態。
5. 驗證 Git Diff、Commit、回溯及跨 AI 重讀一致性。

### P2｜逐批擴展

試跑通過後逐批建立其他模組，更新 PROJECT.md 索引與相依關係；檢查重複規格、斷裂引用、過期追蹤資料。

### A 階段完成標準

- 每個模組有唯一正式規格。
- AI 能分析新 MD、產生具來源差異，重大變更不擅自生效。
- 三版本獨立，未測試不得標示通過。
- ChatGPT／Claude 共用 AGENTS.md（必要時明確提示）。
- Git Commit 可追溯變更，不需 GitHub Actions。

## 8. B 階段銜接（僅預留）

未來 B 階段可讀取 module_id、需求 ID、三版本欄位、核准狀態及 Commit，經 PHP Cron 匯入 MySQL 並顯示網頁。A 階段不先建 DB 或服務，只維持穩定的 Markdown 欄位與 ID。

## 9. 研究依據與採用界線

- [GitHub Spec Kit](https://github.com/github/spec-kit)：借用規格→計畫→實作→驗證的概念，不整套安裝。
- [OpenSpec](https://github.com/Fission-AI/OpenSpec)：借用正式規格與變更提案分離的概念，不整套安裝。
- [GitHub Docs — About pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)：重大變更可用 PR 審核。

**決議摘要：** 只做 A 階段；一份 AGENTS.md；六類文件；PROJECT.md 合併總覽、架構、路線圖、模組索引；tracking 合併三版本及 Feedback；一模組一份正式規格；重大變更必須核准。
