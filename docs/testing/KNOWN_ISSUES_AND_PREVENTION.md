# Shared Modules V1 MVP｜已知錯誤、防止重犯及狀態追蹤（2026-11-01）

> **此檔是跨對話的問題知識庫**。依據使用者上傳的 `1101_ubuntu_mvp_debug2142(2).txt`（Ubuntu 實機過程，約 436 行）以及後續實測 BatchF 記錄。這是**錯誤／證據／預防紀錄，不是新的正式業務規格**。新增錯誤應先搜尋本檔、避免重複排查。必須區分 FIXED／OPEN／VERIFY；未經回歸不可自動結案。

## 新 AI 必須遵守的排錯閉環
1. 先讀 [AI 規則](../../AGENTS.md)、[專案目標](../../PROJECT.md)、本檔及 [Ubuntu Debug 交接](../handoff/ubuntu_mvp_debug_20261101.md)。
2. 遇到錯誤，先用錯誤訊息、類名、資料表、測試名稱搜尋本檔；對照本機**目前**程式與證據，不能照舊日誌重新修已修問題。
3. 只讀確認現況 → 找根因 → 確認業務契約 → 備份最小修正 → syntax/lint → 目標測試 → 全量回歸。
4. 每條記錄格式：問題 ID、症狀、**證據與時間**、根因確定程度、正確解法、禁止捷徑、改動檔案、測試結果、狀態；每次更新保留歷史，不把修過誤稱已驗收。
5. 不可為綠燈刪測試、停用 `TestDatabaseGuard`、關外鍵、繞過模組依賴、取消報名截止或覆蓋 Ubuntu 本機 ZIP。

## A. 已找到根因或完成局部修復

### BUG-001｜兩種 Shared Modules 原始碼混淆 — FIXED（環境工作區已隔離；源碼差異持續提醒）
- 舊 `/home/media/GitDev/ShareModules` 是 GitHub PoC；新 `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/` 是 ZIP MVP 驗收工作區。
- **禁止**把舊 GitHub main、PR #63、staging ZIP 或不同時點 ZIP 解壓覆蓋本機 MVP。
- 初期 Docker `/workspace` 掛舊 PoC；後來已改為 MVP `backend`。見環境文件。再次操作前可唯讀檢查 mount，不必重裝 Docker。

### BUG-002｜Composer/vendor 最初不存在 — FIXED
- 本機已安裝 `vendor`、產生 `composer.lock`；Laravel 13.39.0／Pest 4.7.8 已可執行。
- 不要重複要求安裝 Composer、PHP 8.4、MySQL 或另建 Docker。只有當現況證明缺少時再處理。

### BUG-003｜MySQL migration 1419，Trigger 無法建立 — FIXED（25/25 Migration PASS）
- 原因：MySQL Binary Logging + 一般帳號無權建立相關 Trigger/function。
- 當時以 MySQL `log_bin_trust_function_creators` 方向排除，**不能假定該設定永久存在或無安全代價**；重新部署時先查驗。
- 保留保護 `audit_events` 的 Trigger，不能為通過 migration 刪除安全設計。

### BUG-004｜MySQL 保留字 `interval` — FIXED（Migration 25/25 PASS）
- `calendar_recurrences.interval` 在 MySQL 8.4 需要正確轉義／合理對應；舊失敗已排除。
- 不要無故修改整套 Schema 或重置既有驗收資料庫。

### BUG-005｜Docker `compose.yaml` YAML 縮排錯誤 — FIXED
- `DB_PASSWORD` 曾多 4 空白，`docker compose config --quiet` 後已 PASS。
- 不要無故重建 iStudy、MySQL 或其他容器。變更 Compose 前先備份並限制於 Shared Modules PHP 設定。

### BUG-006｜Pest 指向錯誤 DB／Guard 拒絕 — FIXED（保留永久防護）
- `phpunit.xml` 的 `DB_DATABASE` 與 `DB_TEST_EXPECTED_DATABASE` 曾不一致；Docker 一般環境使用 `shared_mvp_test`，但 Pest **只能使用 `shared_pest_test`**。
- 每次 Pest `docker exec` 明確加 `APP_ENV=testing`、`DB_CONNECTION=mysql`、`DB_DATABASE=shared_pest_test`、`DB_TEST_EXPECTED_DATABASE=shared_pest_test`。
- `ConcurrencyTestCase` 使用 `migrate:fresh`；`BatchGRaceTest` 涉及測試建／刪表。**不能停用 TestDatabaseGuard**。
- 詳見 [Pest 安全指令](pest_database_safety.md)。

### BUG-007｜`tests/TestCase.php` 載入不存在的 `.env` 造成 91 Warning — FIXED（須回歸確認）
- 先前 `/workspace/.env` 不存在造成大量 Warning；後續 Unit／Schema 已能執行 PASS。
- 請以本機檔案確認存在與權限；不應為此重建整套環境，不要將 `.env` 及秘密上傳 GitHub。

### BUG-008｜Forms `$open` transaction closure 作用域 — FIXED（局部測試驗證）
- `app/Actions/Forms/FormSubmissions.php` 的 `publish(..., bool $open)` 定義了 `$open`，卻沒加入 `DB::transaction(function () use (...))`。
- 已補 `use ($open)`，2026-11-01 PHP lint PASS；BatchF 不再出現 undefined variable。
- 本機曾建立 `FormSubmissions.php.bak_20261101_213529`。
- 注意：整套 Feature **尚未在此更改之後回歸**。

### BUG-009｜Query Builder `DB::table(...)->whereKey(...)` 誤用 — FIXED（局部驗證）
- `tests/Feature/BatchFTest.php` 用 `DB::table('calendar_events')->whereKey($id)` 與 notifications 類似語法，SQL 執行成 `WHERE key = N`；表無此欄。
- **只針對確認主鍵是 `id` 的 Query Builder**改為 `->where('id', $id)`；Eloquent Model 的 `->whereKey()` 是合法 API，不可全域盲目替換。
- 已修 BatchF **11 處**；lint PASS，SQL `key` 失敗在下一輪消失；BatchF 從 **17 PASS／14 FAIL** 改為 **20 PASS／11 FAIL**。
- 注意：不代表所有 Feature 或其他檔案皆已檢查。

## B. 開放中的缺陷（OPEN／需實機調查）

| ID | 模組／症狀 | 最新證據 | 建議排查（不可假定根因） |
|---|---|---|---|
| OPEN-001 | ATTEND-01 ×2 + F02 ×1：「報名已截止」 | 最新 BatchF `CreateBooking.php:119` | 測試在活動已開始後嘗試報名；核對 fixture 時間、報名截止和既定政策，**不可放寬正式規則** |
| OPEN-002 | NOTIFY-02 ×1：期望 FCM 送 2 台但送 0 台 | BatchF `BatchFTest.php:279` | 檢查 User active/family、PushPreferences、DeviceToken、模組 gate、claim 與實際 sender |
| OPEN-003 | F03 ×1：舊 lease 接管後預期拋例外但未拋 | BatchF `BatchFTest.php:385` | 同 OPEN-002，查在 FCM 發送前是否因偏好/帳號結束 |
| OPEN-004 | R05 ×1：期望 tok-1 實際無發送 | BatchF `BatchFTest.php:499` | 同 OPEN-002，核對 lease 所有權和 renew 不可被放寬 |
| OPEN-005 | SHOP-01 ×1／F01 ×2／R01 ×1：MODULE_DISABLED | BatchF `ModuleRegistry.php:160` | 檢查 `ModuleRegistry::state()`、依賴、required config、`bfModule` fixture；**不可移除 module gate** |
| OPEN-006 | R03/R04 ×1：CSV 稽核事件 actor_id=0 FK 失敗 | BatchF `FormResource::exportCsv():202` | `(int) auth()->id()` 在未登入時變 0；檢查管理員身分、測試登入和 Audit 契約；**不可關 FK 或虛構 0 號 User** |
| OPEN-007 | Page Atlas `mobile/lib/app.dart` 不存在／同步與 lock 測試 | 先前完整 Feature 失敗 | 核對 `/project` 掛載、repo root 與測試 fixture，保持安全路徑驗證 |
| OPEN-008 | ServiceSlot currency NULL；`calendar_events.currency` NOT NULL | `GenerateServiceSlots.php:91` | 查模板 currency 預設與 DB 契約，不可盲目補假資料 |
| OPEN-009 | Catalog series validation、scope 錯誤文字 | 先前完整 Feature 失敗 | 查驗證欄位與已核准業務規格，避免單純更改訊息掩蓋錯誤 |
| OPEN-010 | Content public post、comment blacklist、favorites | 先前完整 Feature 失敗 | 檢查已發佈狀態、開放留言、篩選、權限設定 |
| OPEN-011 | Booking `DueSource::Promotion` enum 與字串預期 | `BookingFlowTest.php:55` | 區分測試斷言與正式 enum casting，不要為斷言改業務契約 |
| OPEN-012 | MEDIA 18 MiB 上限錯誤訊息 | `Decision1031Test.php:90` | 18 MiB = 18,874,368 bytes；核對真正 validator 與錯誤 |
| OPEN-013 | GProfiles `Target class [config] does not exist` | `GProfilesTest.php:35` | Laravel app/bootstrap/test 初始化問題需查明 |
| OPEN-014 | ModuleSwitch 無法停用依賴模組 | 舊 Feature，依賴「標籤候選、合併與相關內容」 | 先停依賴者或改合理 fixture；保留拒絕保護 |

## C. 測試結果不可錯認
- MySQL Migration：**25/25 PASS**。
- Pest Unit：**43/43 PASS，99 assertions**（當時的實測）。
- Pest Schema：**91/91 PASS，120 assertions**（當時的實測）。
- 完整 Feature：**舊輪 114 PASS／46 FAIL，455 assertions**；後續修改後**尚未重跑**，不能標示最新 PASS。
- BatchF：**最新 20 PASS／11 FAIL，104 assertions**。11 FAIL 分布：3 報名截止、3 推播、4 模組開關、1 CSV Audit。
- Concurrency：**NOT RUN**。
- Flutter Analyze/Test/Android：此份 Ubuntu 日誌**沒有驗收證據**；不得宣稱通過。

## D. 其他歷史提醒
- `shared_dev` 和舊 `ShareModules` 曾被提議日後刪除，但**不是本次 GitHub 文件更新的刪除授權**，不要執行。
- 後續產生檔案**用英文檔名**；Ubuntu 設定修改優先提供一段可複製指令，先備份、檢查。
- 不能把 Claude 雲端「靜態審查」當成 Ubuntu 實機測試；不同 ZIP 快照與本機修改要建立來源對照。
- 不要為了綠燈新增無關套件、設 CI、重建 Docker、改 MySQL 或不停累積零碎 Commit。
- DevApp_md `AGENTS.md` 管理 MD 的版本／審查流程；正式模組需求由 `modules/` 與最新 `ChangeLog/` 控制。

## E. 更新本檔的結案門檻
將一項 OPEN 改為 FIXED 必須附：修正檔案、具體原因、相應測試名稱、實機 PASS 時間；完整回歸未通過仍應註記 regression pending。**CI 綠燈與 AI 宣稱成功都不能代替實機業務驗收。**

## 2026-11-02 最新排錯索引（優先閱讀）
- **[DEBUG_PLAYBOOK_20261102.md](DEBUG_PLAYBOOK_20261102.md)**：22 類症狀→根因→正確處理→禁止捷徑，涵蓋 Round1–11、Push MySQL 同秒租約更新、Woo 分頁、Page Atlas 隱含提交、媒體 OOM、測試 fixture 與 TestDatabaseGuard。
- **[UBUNTU_MVP_VERIFIED_20261102.md](../environment/UBUNTU_MVP_VERIFIED_20261102.md)**：已驗證環境、容器／DB、備份與回歸命令。
- **最新 Feature 實測 162 PASS／0 FAIL、691 assertions、46.70 秒**（Round11）。本檔早期 OPEN/VERIFY 條目是歷史排查紀錄，遇到同名問題時先查上述新索引和 Ubuntu 目前程式，不能直接當成仍失敗。Feature 全綠不代表 Concurrency／Flutter／外部整合／正式部署已驗收；不因綠燈自動結案。

## F. 2026-11-02 重啟後重犯問題與已驗證預防（最新）

### BUG-010｜Ubuntu reboot 後 PHP 容器未運行 — FIXED（restart policy 已設，重開機效果待實測）
- 症狀：`container ... is not running`，Unit/Schema 當次未執行，不是測試斷言失敗。
- 處理：既有 `eachcore-dev-php-1` 啟動成功；兩容器均設定 `unless-stopped`；`docker.service` 顯示 `enabled`。禁止重建或重裝。

### BUG-011｜MySQL 未啟動，PHP 無法解析 mysql — FIXED（本輪 preflight PASS）
- 症狀：Schema 91 個案例皆報 `php_network_getaddresses: getaddrinfo for mysql failed`。
- 處理：啟動既有 `eachcore-dev-mysql-1`；本機 `shared-test.sh` 已驗證 MySQL readiness、PHP DNS 與 Laravel 測試 DB 連線全部 PASS。
- 不得將同一環境錯誤誤報為 91 個 Schema 程式缺陷。

### BUG-003 復發｜MySQL 1419 Trigger 建立失敗 — FIXED（持久化設定已驗證）
- 症狀：Migration `2026_11_02_000012_protect_audit_events.php` 建立 `trg_audit_events_no_update` 時，MySQL Binary Logging 開啟且一般帳號無 SUPER。
- 2026-11-02 使用者核准並執行 `SET PERSIST log_bin_trust_function_creators = ON`；回報 `log_bin_trust_function_creators=ON`、`log_bin=ON`。保留 audit_events append-only Trigger；不授 SUPER、不關 Binary Logging。
- **測試前置檢查入口**：Ubuntu 本機 `/home/media/GitDev/ShareModules-MVP-Test/shared-test.sh`；先確認既有 PHP/MySQL 容器、DNS、readiness、trigger 設定與僅 `shared_pest_test` 可達，再執行 Pest。腳本未提交到程式 Git 倉庫。
- **實機回歸**：Schema **91 PASS／120 assertions／19.19s**；此前 Feature **165 PASS／701 assertions**、Unit **43 PASS／99 assertions**。合計 **299 PASS／920 assertions**。Concurrency／Flutter／Android 未驗收；容器 restart policy 重開機效果待再次 reboot 實測。
