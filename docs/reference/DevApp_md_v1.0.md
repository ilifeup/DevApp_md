# DevApp_md｜AI 智慧計畫書管理（A 階段）精簡計畫書 v1.0

> 規劃日期：2026-11-01  
> GitHub：https://github.com/ilifeup/DevApp_md  
> 狀態：**規劃草案，尚未建立或修改 GitHub 檔案**  
> 範圍：**A｜ChatGPT Plus／Claude Pro → 可用 GitHub 工具或 API → GitHub Markdown**  
> 不包含：B 階段 PHP Cron、MySQL、Web，也不建置自訂 MCP Server、AI Agent 或 GitHub Actions。

## 1. 目標與原則

將多份 App 開發文件變成一套**AI 易讀、易比對、可持續迭代、具決議約束**的正式 Markdown 計畫書。

1. **一個 `AGENTS.md` 管所有 AI**：ChatGPT、Claude 及其他 AI 都以此為共同規則；**不建立 `CLAUDE.md`**。不同工具是否自動讀取 `AGENTS.md` 不保證，執行任務時應明確指示先讀取它。
2. **一模組一份正式規格**：每個模組唯一正式來源為 `modules/<MODULE-ID>.md`，其他文件只引用，不複製規格。
3. **三版本軸分開**：需求規格、程式實作、測試驗收各有自己的版本／狀態／證據，不能以文件已更新推論程式已完成，也不能以 CI 綠燈推論驗收通過。
4. **新資料不直接覆蓋正式決議**：AI 先比對、提出差異，依權限決定自動更新或待核准。
5. **少檔案、少重複、少維護**：先使用 GitHub Markdown、Commit 與 Diff，不做重型文件系統。
6. **正式規格與開發程式分離**：本儲存庫只存計畫與證據連結，不搬入完整 Flutter／Laravel 程式。

## 2. 精簡文件結構：6 種文件職責

```text
DevApp_md/
├── AGENTS.md                # 所有 AI 共用的最高規範
├── README.md                # 入口、索引、使用方法
├── PROJECT.md               # 專案目標、MVP、架構、路線圖（合併管理）
├── modules/
│   ├── MEDIA-01.md          # 模組唯一正式需求規格
│   ├── M05.md               # 其他模組，依正式模組 ID 命名
│   └── ...
├── changes/
│   └── CHG-YYYYMMDD-001.md  # 僅有變更時才建立；提案、核准、差異
└── tracking/
    ├── MEDIA-01.md          # 同一模組的三版本軸與證據
    └── ...
```

**六類職責：** AI 規範、文件入口、專案總覽、正式模組規格、變更紀錄、三版本追蹤。`README.md` 只做索引；`PROJECT.md` 合併原先的 PROJECT／ARCHITECTURE／ROADMAP／MODULES；`tracking/` 合併原先的 implementation／verification／feedback。**不另建** `CLAUDE.md`、`ARCHITECTURE.md`、`ROADMAP.md`、`MODULES.md`、`CHANGELOG.md`、`docs/decisions/`、`docs/contracts/`、`docs/feedback/` 等獨立檔案或目錄。

**建立時機：** 首次只建立 `AGENTS.md`、`README.md`、`PROJECT.md`、`modules/MEDIA-01.md`、`tracking/MEDIA-01.md`。有實際變更才新增 `changes/CHG-*.md`；其他模組按需建立。空資料夾不必預先建立。

## 3. 各文件內容規範

### 3.1 `AGENTS.md`：唯一 AI 規範

內容至少涵蓋：

- 優先讀取順序：`AGENTS.md` → `README.md` → `PROJECT.md` → 目標模組 `modules/` → 對應 `tracking/` → 相關 `changes/`。
- 正式規格的來源、模組 ID 命名、固定需求 ID、Markdown front matter 格式。
- 禁止未經核准新增／刪除功能、API、資料表、權限、重要業務規則；禁止推翻正式決議。
- **C 模式**：可自動處理有證據的一般進度與非語意修改；重大規格變更需明確取得使用者核准。
- 變更必須先做差異比對，輸出 `AUTO_APPLY`／`REVIEW_REQUIRED`／`CONFLICT`／`NO_CHANGE`。
- 不可把「AI 宣稱已完成」當成程式已完成的證據；不可把「測試案例存在」當成測試已通過。
- 更新只限受影響文件，避免碎片化 Commit；不得擅自合併 PR 或操作正式資料庫。
- ChatGPT／Claude 若未自動讀取 `AGENTS.md`，使用者任務指令應明確要求先讀取。**單一規範文件不等於所有 AI 平台原生自動識別**。

### 3.2 `README.md`：入口與索引

內容：本專案用途、A 階段操作流程、目前專案清單、模組索引連結、最近待核准變更連結、如何交付新 MD。避免複製正式規格。

### 3.3 `PROJECT.md`：合併式總計畫

固定章節：

1. 專案定位與目標
2. 使用者、MVP 範圍及不做的事
3. 技術堆疊與架構（只描述已核准設計）
4. 模組總表與相依關係（連結 `modules/`）
5. 開發里程碑、優先順序、阻礙
6. 跨模組共用原則（詳細內容仍以所屬模組為準）
7. 待決策事項與參考連結

### 3.4 `modules/<ID>.md`：唯一正式需求規格

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

章節：模組目的／範圍、角色權限、功能需求（固定 `REQ-ID`）、核心流程、UI 與 API／資料契約、驗收條件、明確排除功能、已核准變更引用。

**範例：MEDIA-01 的已知正式方向**：音訊先下載完整 AES-256-GCM 加密檔再於本機播放；管理端上傳上限 18 MiB；不得擅自加入伺服器端解密串流／線上直接播放。實際寫入時仍須核對已核准來源，不以本計畫書替代完整模組規格。

### 3.5 `changes/CHG-*.md`：一次變更一份紀錄

新 MD 或 AI 開發回報導入時建立，內容包括：

- 來源（檔名、日期、來源網址或 Commit SHA）及影響模組。
- 逐項差異：新增／修改／刪除／重複／衝突。
- 對應需求 ID、原正式規則、新提案、原因與風險。
- 決策：`proposed`／`approved`／`rejected`／`superseded`。
- 核准人、核准日期與核准依據（如適用）。
- 更新結果：修改了哪些正式檔案、對應 Git Commit。

**未核准提案不得寫入正式模組規格。** 已核准變更的正式規則只寫入 `modules/`；`changes/` 留存歷史與差異，不維護第二份完整規格。

### 3.6 `tracking/<ID>.md`：三版本管理與 Feedback

同一份追蹤檔保留三個**彼此獨立**的區塊：

| 版本軸 | 記錄內容 | 合格證據 |
|---|---|---|
| A. 需求規格版 | `spec_version`、核准狀態、規格 Commit | 正式 `modules/<ID>.md`、核准變更 |
| B. 程式實作版 | `implementation_version`、狀態、程式 Repo／Commit、已覆蓋需求 ID | 實際程式變更、審查紀錄 |
| C. 測試驗收版 | `verification_version`、狀態、測試時間、案例、結果、證據 | 測試輸出、實機驗證、人工驗收 |

最後加一節 **Feedback／待處理差異**，例如：需求已核准但尚無程式證據；實作已有 Commit 但尚未驗收；程式宣稱支援已禁止功能。Feedback 是待查提醒，不能自行改寫正式需求。

三個版本號可以不同；版本號與狀態是兩種不同欄位。沒有證據時填「待確認／未驗證」，不得猜測。

## 4. AI 更新標準流程（A 階段）

```text
使用者交付新 MD／開發報告／Commit 連結
                ↓
AI 讀 AGENTS.md、PROJECT.md、目標模組及追蹤檔
                ↓
辨識模組 ID／需求 ID／來源版本，與正式規格比較
                ↓
產生差異分類與變更紀錄（changes/CHG-*.md）
                ↓
      ┌─────────┴──────────┐
      ↓                    ↓
一般進度／非語意更新     業務規則／API／DB／權限變更
有來源證據可更新         必須先由使用者核准
      └─────────┬──────────┘
                ↓
只更新受影響的 modules/、tracking/、PROJECT.md
                ↓
檢查連結、需求 ID、版本軸及是否違反核准決議
                ↓
集中一次 Commit，回報 SHA、修改清單與未解決問題
```

**失敗處理：** 無法確認來源、版本或核准狀態時，不更新正式規格；保留變更提案並標示 `CONFLICT` 或 `REVIEW_REQUIRED`。GitHub API／工具無寫入權限時，AI 只產出待提交檔案與差異，不宣稱已完成 GitHub 更新。

## 5. AI 更新權限（C 模式）

| 變更 | AI 自動更新 | 說明 |
|---|---|---|
| 開發進度、程式 Commit、測試證據 | 有可靠來源時可以 | 寫 `tracking/`，不可冒充正式驗收 |
| 索引、錯字、連結等非語意修正 | 可以 | 不得改變業務意義 |
| 新增功能或變更既有功能 | 不可以 | 先建立變更提案並取得核准 |
| 刪除功能、API、資料表或修改權限 | 不可以 | 先核准，並評估相依影響 |
| 改變架構、重要流程或正式決議 | 不可以 | 先核准 |
| 修改 `AGENTS.md` 核心規則 | 不可以 | 先核准 |
| 自動合併 PR、正式部署、操作正式 DB | 不可以 | 不在本計畫授權範圍 |

## 6. AI 工具與 GitHub 使用方式

- **ChatGPT Plus**：負責需求整理、比對、風險審查、差異提案及在具備權限時更新 GitHub；若目前對話未具備可寫入工具，改交付可提交的 Markdown。
- **Claude Pro**：可依相同 `AGENTS.md` 執行分析、開發回報整理與 GitHub 更新；具體可用操作取決於所連接工具的權限。
- **GitHub**：唯一正式 Markdown 儲存來源；使用 Commit／Diff 作為歷史。重大變更建議走分支／PR 審核，但不要求使用 GitHub Actions。
- **跨 AI 交接**：提供 Repo URL、目標模組 ID、來源 MD、需執行任務，並要求 AI 先讀 `AGENTS.md`。不要每次傳整份巨大 HTML。

**可複製的通用任務指令：**

> 請先讀取 `https://github.com/ilifeup/DevApp_md` 的 `AGENTS.md`、`README.md`、`PROJECT.md`，再讀取本次目標模組 `modules/<ID>.md`、`tracking/<ID>.md` 與相關 `changes/`。分析我提供的新 MD，依固定需求 ID 逐項比較，先報告新增、修改、刪除、衝突與來源證據。一般有證據的進度可依規則更新；涉及正式規格、API、資料庫、權限或重要決議者先等我核准。不得新增未核准功能；完成後回報實際修改檔案與 Git Commit SHA。沒有 GitHub 寫入權限就提供待提交內容，不得宣稱已提交。

## 7. 實施階段與驗收

### P0｜建立最小骨架

建立 `AGENTS.md`、`README.md`、`PROJECT.md`、`modules/MEDIA-01.md`、`tracking/MEDIA-01.md`。先只做一個模組，確定格式可讀、可更新；不要一次產生 45 個空白文件。

### P1｜MEDIA-01 試跑

1. 以正式核准的 MEDIA-01 內容建立規格與需求 ID。
2. 輸入一份新 Claude MD（含進度與可能衝突的建議）。
3. AI 產出 `changes/CHG-*.md`，正確辨識禁止線上串流等衝突。
4. 不經核准不得更動正式規格；可獨立更新有證據的實作／測試狀態。
5. 驗證 Git Diff、Commit、回溯、跨 AI 重讀是否一致。

### P2｜逐批擴展

試跑通過後才逐批建立其他模組，並在 `PROJECT.md` 增加索引與相依關係。每批檢查重複規格、斷裂引用與過期追蹤資料。

### A 階段完成標準

- 任一模組可以從索引找到唯一正式規格。
- AI 能對新 MD 產生具來源的差異，重大變更不擅自生效。
- 三版本狀態獨立，未測試不得標示驗收通過。
- ChatGPT／Claude 都能依單一 `AGENTS.md` 操作（必要時明確提示讀取）。
- Git Commit 可追溯變更，且不需要 GitHub Actions。

## 8. B 階段銜接（僅預留，不開發）

未來 B 階段可從 GitHub 讀取 `module_id`、需求 ID、三版本欄位、核准狀態與 Commit，經 PHP Cron 匯入 MySQL 並展示網頁。A 階段**不為 B 階段先建資料庫或服務**；只維持穩定的 Markdown 欄位與 ID 即可。

## 9. 研究依據與採用界線

- [GitHub Spec Kit](https://github.com/github/spec-kit)：借用規格→計畫→實作→驗證的階段概念，不整套安裝。
- [OpenSpec](https://github.com/Fission-AI/OpenSpec)：借用「正式規格與變更提案分離」的概念，不整套安裝。
- [GitHub Docs — About pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)：重大變更可用 PR 進行審核。

以上為適用於 DevApp_md 的**精簡設計建議**，不是宣稱上述工具原生具備本計畫全部功能。

---

**本版決議摘要：** 只做 A 階段；只用一份 `AGENTS.md`；從原先 10 類精簡為 6 類；`PROJECT.md` 合併總覽／架構／路線圖／模組索引；`tracking/` 合併三版本與 Feedback；正式模組規格仍一模組一檔；重大變更必須核准。