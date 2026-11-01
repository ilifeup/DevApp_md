# Ubuntu MVP｜Claude Code 接手、環境驗證與 App-T 上架路線（2026-11-02）

> 本文記錄使用者於 Ubuntu 終端機提供的**實機驗證結果**和下一階段執行計畫。DevApp_md 為文件儲存庫，不是 Round11 程式碼儲存庫。參照 [AGENTS.md](../../AGENTS.md)、[PROJECT.md](../../PROJECT.md)、[Ubuntu 既有環境快照](../environment/UBUNTU_MVP_VERIFIED_20261102.md)、[測試資料庫保護](../testing/pest_database_safety.md)、[Debug 防重犯手冊](../testing/DEBUG_PLAYBOOK_20261102.md)。不得把計畫誤寫成實測成功。

## 一、專案目標與進度

- 共用規格 **47 模組：P0 33／P1 10／P2 4**；來源為最新正式決議，優先於歷史 r7.2 的 45 模組版本。
- Flutter 共用元件、Laravel API、Filament 後台共用程式碼；**三 App 各自部署、各自 MySQL DB、各自 Filament**，不是一套中央共用會員資料庫。
- **App-T 第一個完成完整 MVP → 測試 → 上架 → 維護**，再將已驗證的共用能力用於 App-W 和第三 App；第三 App 商務採 WooCommerce 對接。不得為了加速一次重寫三套 App。
- 最新實機後端 Feature：**162 PASS／0 FAIL、691 assertions、46.70s**；證據 `mvp-feature-final-round11-20261102-024503.zip`。僅代表 Feature suite，**非整體 MVP PASS**。
- 歷史 Unit 43 PASS／99 assertions；Schema 91 PASS／120 assertions；Migration 25 PASS。**Round11 最終基線未重跑 Unit/Schema**；Concurrency NOT RUN；Flutter、Android、iOS、FCM 實際發送、外部 Woo、E2E、正式環境 NOT RUN。
- `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1` 是 ZIP 工作樹、**沒有 .git**。舊 `/home/media/GitDev/ShareModules` 為不同 PoC Git 基線；禁止拿它直接覆蓋 Round11。檔案快照及 SHA-256 待執行，不能宣稱已完成。

## 二、Ubuntu 已驗證環境（2026-11-02）

| 分類 | 已確認 |
|---|---|
| OS | Ubuntu 26.04.1 LTS x86_64 |
| Claude Code | v2.1.316，Claude Pro 訂閱授權登入成功，Opus 5.5 畫面 |
| Node/npm/git | v24.22.1／11.19.1／2.53.0 |
| Docker/Compose | 29.8.2／v5.6.0 |
| PHP/MySQL | PHP 8.4.27 容器／MySQL 8.4.13；Laravel 13.39.0、Pest 4.7.8 |
| Flutter/Dart | 3.47.8 stable／3.13.7；Android SDK 36.0.0、Java 17.0.20.1 |
| PHP 容器 | `eachcore-dev-php-1`，Laravel `/workspace`，有讀寫掛載 |
| MySQL 容器 | `eachcore-dev-mysql-1` |
| Compose | `/home/media/GitDev/DevEnv/compose.yaml`，**不修改** |
| MVP | `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1`，含 `backend/`、`mobile/`、`docs/`、`tools/` |
| Flutter | 實際程式目錄是 `mobile/`，**不是舊文件所列 flutter/**；`mobile/android`、`mobile/test` 存在；`mobile/ios`、`mobile/integration_test` 尚未看到 |
| Claude 審查帳號 | `claude_audit` UID 1001、專屬 HOME；不在 sudo/docker/lxd/adm 群組；Docker socket 無權限 |
| 唯讀程式 | `claude_audit` 已能讀 README、Laravel artisan、Flutter pubspec；backend/app 和 mobile/lib 無寫權；backend/.env.testing 無讀權 |
| 報告位置 | `/home/claude_audit/reports/shared-modules-mvp/`，帳號有寫入權 |
| Claude啟動 | `/home/media/.local/share/claude/versions/2.1.316 --permission-mode plan`，授權登入、專案信任、`pwd && ls -la` 讀取成功 |

上述為已驗證項目，不代表整個檔案樹已全面隔離，也不代表可由 Claude 無限制呼叫 Docker。**iStudy 在同機正常運作是最高操作限制**：不得操作／停止／重啟其容器、PostgreSQL、服務、媒體檔、工作目錄；不可進行全機 Docker 清理、全域重啟、修改 Compose、重設網路或更動宿主機 Docker 權限。

## 三、Pest 資料庫實測

- Pest 唯一 DB：`shared_pest_test`。`shared_mvp_test` 為另一驗收 DB，不可跑 Pest。
- 新增 **`shared_pest_runner`@'%'**，授權僅 `shared_pest_test`.* ALL PRIVILEGES；驗證登入測試 DB 成功、嘗試 `shared_mvp_test` 回 `ERROR 1044`（預期拒絕）。舊 `shared_mvp` grants 未更改。
- Laravel 實測明確覆蓋 APP_ENV=testing、DB_CONNECTION=mysql、DB_DATABASE=shared_pest_test、DB_TEST_EXPECTED_DATABASE=shared_pest_test、DB_USERNAME=shared_pest_runner 與互動輸入的密碼：Configured DB、Actual DB 都是 `shared_pest_test`，CURRENT_USER=`shared_pest_runner@%`，PASS。
- PHP 容器**預設仍為 shared_mvp**，不得因設定 `APP_ENV=testing` 就假設已切換專用帳號；新帳號與密碼必須在受控測試入口明確注入（不得留於 Git、報告、CLI 歷史）。現有 `backend/.env.testing` 的 DB Guard 和 `backend/phpunit.xml` 需保留。
- Concurrency 使用 `migrate:fresh`、TRUNCATE 和暫存表；建立受控測試入口、驗證實際 DB 與帳號後才可運行。容器以 root 執行且對 `/workspace` 可寫，**不得將不受限的 docker exec 作為 Claude Code 的自動化權限**。

## 四、八階段開發路線及驗收

下列工期是**單人＋Claude Code 主力、已有共用程式前提下的初估工作天**；須在盤點 33 P0 後修訂，非交付承諾，階段可部分交疊。

| 階段 | 驗收標準 | 初估工作天 | 目前狀態 |
|---|---|---:|---|
| 1 後端品質、DB | 固定基線 SHA-256；Unit/Schema/Feature 回歸；Concurrency；Guard、交易與權限證據 | 3–6 | Feature PASS，其他待驗 |
| 2 API 與 Filament | 33 P0 每項 Route→Controller→Service→Model→DB 追溯、Filament CRUD／角色及 Page Atlas 可操作 | 5–10 | 待盤點 |
| 3 Flutter 共用基礎 | 登入、導航、API、權限、通知、錯誤處理、共用 UI 可實測 | 5–10 | 待盤點／補齊 |
| 4 App-T MVP 整合 | 依核准範圍，會員、文章、活動、報名、簽到、影音等跨層流程驗收 | 8–18 | 待盤點／整合 |
| 5 E2E 與真機 | Flutter↔Laravel↔MySQL、Android 真機、FCM、適用金流及 iOS 條件驗證 | 7–14 | NOT RUN |
| 6 部署與安全 | HTTPS、隔離部署、備份還原演練、監控、個資與權限測試 | 4–8 | NOT RUN |
| 7 封閉測試／商店審查 | Google Play 測試要求、TestFlight、商店內容／政策符合 | 14 天測試期＋3–7 工作天準備；審核時間不保證 | NOT RUN |
| 8 正式上架／維護 | Android AAB、iOS IPA、發佈核准、監控及修復交接 | 2–5＋平台審核時間 | NOT RUN |

> iOS 建置／簽署通常需 macOS/Xcode；Ubuntu 完成 Android 不代表 iOS 可直接上架。Google Play／Apple 當期政策要在階段 7 重新查證。

## 五、Claude Code 主力工作配置

| 任務 | 自動化配置 | 核准與界線 |
|---|---|---|
| Round11 原始碼快照／SHA-256／Git 基線提案 | 高 | 產出清單及 hash；**不自動 Git Commit／PR／Merge**；避免納入 .env、密鑰、vendor 等 |
| Unit／Schema／Feature | 高 | **只用受控測試入口、shared_pest_runner、shared_pest_test** |
| Concurrency | 條件式 | 確認 Guard、專用 DB、DB 範圍後執行 |
| 33 P0 模組差異與跨層盤點 | 高 | 逐項註記需求證據、API、Filament、Flutter、測試和缺口 |
| Flutter analyze/test/Android build | 高（環境確認後） | 不安裝無關套件、不碰 iStudy |
| 真機 UI、業務驗收 | AI 輔助 | 由使用者決定通過與否 |
| 缺口分級與工作天 | AI 提案 | 人工核准重大需求變更 |
| Git 提交／PR／合併、正式 DB／部署／上架 | **不自動授權** | 需另外明確批准 |

**執行次序：**先更新 DevApp_md 文件與確認規則→取得 Round11 唯讀源碼清單和 SHA-256→盤點 P0/API/Filament/Flutter 與 App-T 最短路線→建受控測試入口→跑缺口測試（先 Unit/Schema，後 Concurrency）→按相依性集中開發／回歸→逐階驗收。避免為小問題建立零碎 Commit 或重做已驗證功能。

## 六、下一次 Claude Code 任務（先理解與提出執行計畫）

1. 優先閱讀本庫 `AGENTS.md`、`PROJECT.md`、`docs/handoff/`、`docs/testing/`、`docs/environment/`、`ChangeLog/`、`modules/`、`tracking/`，**不得只讀舊的 Debug 摘要**。這個 repo 是文件來源，不是 MVP 程式碼。
2. 對比 Ubuntu ZIP 工作樹 `backend/`、`mobile/`、`docs/` 與本庫需求。區分規格核准、原始碼實作、測試驗收，不以檔名當完成證據。
3. 輸出 Round11 快照計畫、33 P0 五層缺口矩陣、App-T MVP 最小完整路線、階段驗收與工作天更新案。
4. 先報告矛盾與建議修訂，無授權不修改程式、DB、Compose、Git Commit／PR／Merge。
5. 所有 GitHub 寫入遵守 `AGENTS.md` 術語保密與最新決議，不能把歷史 45/46 模組當最終數字。

## 七、文件同步注意

- 原 `docs/handoff/NEW_CHAT_UBUNTU_MVP.md` 中的歷史 20 PASS／11 FAIL、114 PASS／46 FAIL 均不是最新 Round11；以最新驗收證據為準。
- 原環境文件把 Flutter 指向 `flutter/` 的舊路徑；實機已證實 `mobile/`。
- 新的 `shared_pest_runner` 權限與 Claude Code 登入、唯讀帳號驗證應以本篇更新為準；先前文件中不覆蓋 DB_USERNAME 的 Pest 指令**不符合新帳號隔離要求**，不得原樣直接自動化。
- 不變更已核准模組規格，不碰 iStudy 或舊 PoC Git 工作樹。
