# Shared Modules 與三 App｜長期儲存庫及路徑治理方案（2026-11-02）

> 狀態：**儲存庫與文件組織方案**。依既有已核准「共用程式碼、三 App 各自部署／DB／Filament」原則設計；本文**不授權**立即拆分 Round11、改 API／DB 契約、修改程式、部署或自動合併。任何涉及正式架構契約的實作變更，依 [AGENTS.md](../../AGENTS.md) 先核准。此文件只在 DevApp_md 管理索引與證據，不複製全部原始碼。

## 1. 最終版圖：五個邏輯儲存庫

| 邏輯儲存庫 | 建議 GitHub 名稱（尚未確認存在／未建立） | 唯一責任 | 不應存放 |
|---|---|---|---|
| 文件控制中心 | **現有** `ilifeup/DevApp_md` | 需求、決策、追蹤、跨專案介面契約、驗收證據索引、交接 | 正式程式、密碼、完整測試產物 |
| 共用模組 | `ilifeup/Shared_Modules_For_App`（現有名稱，**是否適合承接 Round11 待核對**） | Laravel／Filament 共用模組、Flutter 共用元件、Migration、API 契約、共用測試 | 三 App 正式資料、個別 App 品牌與部署金鑰 |
| App-T | `ilifeup/App-T`（**候選名**） | App-T Flutter 殼、啟用模組、品牌、App 專屬流程、部署設定樣板 | 共用模組的重複複製品、其他 App DB |
| App-W | `ilifeup/App-W`（**候選名**） | App-W 專屬 Flutter 殼、設定與部署樣板 | App-T 的專屬內容與資料 |
| App-EC | `ilifeup/EachCore_App`（**候選名**） | 電商 App 專屬 UI、WooCommerce 整合及部署樣板 | 自建第二套商品／訂單權威資料庫（除非另經核准） |

**名稱為建議，不代表 GitHub 上已建立或已可使用。** 先驗證現有 `Shared_Modules_For_App` 的實際程式與分支，不得把舊 PoC 或 PR 當作 Round11。

## 2. DevApp_md 文件目錄：不搬動現有權威檔案

```text
DevApp_md/
├── AGENTS.md                  # 全庫 AI 規則（現有）
├── PROJECT.md                 # 整體權威入口（現有）
├── modules/                   # 47 個共用模組正式需求（現有，維持唯一來源）
├── tracking/                  # 需求／實作／驗收三版本（現有）
├── changes/                   # 變更提案與核准狀態（現有）
├── ChangeLog/                 # 歷史決議（現有）
└── docs/
    ├── architecture/
    │   └── REPOSITORIES_AND_APP_BOUNDARIES_20261102.md  # 本文
    ├── handoff/               # Ubuntu、Claude Code、跨對話交接（現有）
    ├── environment/           # 可重現環境、版本、隔離（現有）
    ├── testing/               # 測試安全、已知錯誤、證據索引（現有）
    ├── reference/             # 歷史來源，不能取代最新決議（現有）
    ├── shared/                # 未來共用 API／資料契約的索引；需要時才建
    └── apps/                  # 各 App 專屬決策／進度索引；需要時才建
        ├── app-t/
        ├── app-w/
        └── app-ec/
```

**現階段只建立本文**；`docs/apps/` 與 `docs/shared/` 是未來路徑保留，不建立空資料夾或重複文件。共用規格仍以 `modules/<ID>.md` 為唯一權威。每個 App 的索引只記「採用哪些共用模組、版本、例外需求、部署與驗收狀態」，不複製模組卡。

## 3. Ubuntu 本機工作目錄：保留 Round11，不直接搬家

```text
/home/media/GitDev/
├── DevEnv/                              # 現有 Docker Compose；不變更
├── ShareModules-MVP-Test/
│   └── Shared_Modules_V1/              # 現有 Round11 ZIP 工作樹；先保護、快照
│       ├── backend/
│       ├── mobile/                     # Flutter 真正路徑，非 flutter/
│       ├── docs/
│       └── tools/
└── (日後核准建立)
    ├── shared-modules/                 # 新 Git 工作樹，驗證後才匯入 Round11
    ├── app-t/                          # App-T 專屬工作樹
    ├── app-w/                          # App-W 專屬工作樹
    └── app-ec/                         # 電商 App 專屬工作樹
```

以上「日後核准建立」的目錄**目前不宣稱存在**。不得在既有 ZIP 目錄直接 `git init` 並把它當已審核基線；先保存 SHA-256、清理機密與禁用詞、確認檔案歸屬，再在隔離的新工作樹建立版本基線。不得覆蓋 `/home/media/GitDev/ShareModules` 舊 PoC。

## 4. 共用程式如何被三 App 使用

1. **唯一核心來源**：共用 Laravel／Filament／Flutter 模組只在 Shared Modules 維護；App-T／App-W／App-EC 只保留自己的 App shell、啟用設定、少量差異化功能。
2. **版本釘選**：三 App 分別記錄採用的共用模組版本／commit SHA；升級需通過相容性與必要回歸，不自動跟隨共用庫 main。
3. **初期避免過度拆包**：Round11 尚未完成 Flutter 編譯／跨層驗收前，先在現有結構完成 App-T 可用性；後續視實際相依關係選擇 Git subdirectory、Flutter path/git dependency 或 Composer package。**不得直接假設 Laravel 專案可無痛拆成 Composer 套件**。
4. **三套隔離**：每 App 獨立 Laravel 部署、MySQL DB、Filament 後台、環境變數、密鑰、排程、儲存與備份。共用 Migration 定義不等於共用 DB；不得跨 App 查表或加中央 `app_id` 作為隔離替代。
5. **電商邊界**：App-EC 以既有核准 WooCommerce 整合方向為主；商品／訂單／付款的資料權威及同步契約另行明確定義，不憑此文件新增第二套交易資料庫。

## 5. 變更與證據流

- **需求變更**：`DevApp_md/changes/` 提案 → 使用者核准 → `modules/` 更新 → 共用程式庫實作 → 各 App 選擇升級 → 分別驗收。
- **共用 bug**：在 Shared Modules 修一次，記錄根因、測試、修復 SHA；各 App 按影響範圍升級，不複製修補程式。
- **App 專屬 bug**：留在對應 App 程式庫，只有可重用且不破壞共用契約的部分才回饋 Shared Modules。
- **追蹤最小欄位**：模組 ID、需求版本／核准狀態、共用程式 commit、App 採用版本、測試證據、狀態（NOT RUN／BLOCKED／PASS／FAIL）、已知問題連結。
- **禁止假完成**：CI 綠燈、靜態存在、Claude 報告、Feature 162 PASS 均不等於三 App 完成或正式驗收。

## 6. 遷移順序（低風險，避免重做）

| 閘門 | 工作 | 完成條件 | 目前 |
|---|---|---|---|
| G0 文件索引 | 本文及 PROJECT 入口 | 可從 PROJECT 進入本方案 | 本次執行 |
| G1 Round11 保存 | 唯讀 SHA-256、機密／禁用詞掃描、文件與程式清單 | 有可驗證快照且未改原始碼 | 待驗 |
| G2 現有 Git 核對 | 查 `Shared_Modules_For_App` 分支、歷史與 Round11 差異 | 明確決定承接方式；不丟失 PR 成果 | 待驗 |
| G3 App-T 開發基線 | 在隔離工作樹建立可重現 Git 基線，明確區分共用與 App 專屬 | Flutter、Laravel 路徑可追溯，無機密 | 待核准 |
| G4 最小可運行 | 安全測試入口、Unit／Schema／Concurrency、Flutter analyze/test、Android APK | 各項有獨立證據，skip 不算 PASS | 待驗 |
| G5 App-T 完整 MVP | UI→API→DB→UI、後台、真機、部署與商店條件 | App-T 實際可用並通過驗收 | 待驗 |
| G6 共用模組版本化 | 固定可重用 release／契約，再逐步導入 App-W、App-EC | 各 App 釘選版本且隔離部署 | 待核准 |

**不必等待 G6 才設計目錄，但也不為了五個 repo 提前重寫 Round11。** 第一個交付優先順序始終是 App-T MVP。

## 7. 保護措施與待核准決策

- 不操作同機 iStudy、PostgreSQL、既有 Docker Compose、正式 DB、機密；Pest 僅能經受控入口以 `shared_pest_runner` 存取 `shared_pest_test`。
- 本次**不建立**新 GitHub repository、branch、PR，不執行程式碼搬遷、`git init`、Git submodule、Composer／Flutter package 重構或部署。
- 待確認：三個 App 的正式 GitHub repo 名稱與可見性；是否重用現有 Shared Modules repo；共用碼實際分包邊界；App-EC WooCommerce 契約；App-T 上架範圍及簽到 P1 例外。
- **保密規則**：公開文件只用 App-T／App-W／App-EC 和中性識別；禁止把原始受限中文名稱、憑證或機密資訊帶入 repo、Commit、PR 或掃描日誌。

## 8. Claude Code 下一步（避免重複消耗額度）

先閱讀本文、`AGENTS.md`、`PROJECT.md` 與既有 Round11 盤點報告；沿用已完成的 33 P0 缺口矩陣。只需補做「Round11 → 共用庫／三 App」的**檔案歸屬表**，列出真正需搬遷的檔案、保留原位的檔案、可能有相依風險的檔案；不得重新做全庫靜態盤點。任何 Git／程式拆分須另獲核准。
