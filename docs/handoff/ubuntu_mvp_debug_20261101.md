# Ubuntu Shared Modules V1 MVP｜Debug 接手（2026-11-01）

> 本檔只記錄**程式調試進度與待確認缺陷**，不是新規格。遵守本庫 [AGENTS.md](../../AGENTS.md)、[PROJECT.md](../../PROJECT.md)、正式 [modules/](../../modules/) 及最新 [ChangeLog/](../../ChangeLog/) 決議；不得將其他程式庫舊 PoC 當成此 MVP 的原始碼。

## 目前基準與測試
- Ubuntu 上的完整 ZIP MVP 後端在 `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend`。
- 資料庫 Migration **25/25 PASS**。
- Unit **43 PASS（99 assertions）**。
- Schema **91 PASS（120 assertions）**。
- 完整 Feature **歷史測試 114 PASS／46 FAIL（455 assertions）**，**後續修正後尚未完整重跑**。
- BatchF **最新 20 PASS／11 FAIL（104 assertions）**；上一輪 17 PASS／14 FAIL。
- Concurrency **NOT RUN**。
- GitHub `ilifeup/DevApp_md` **只管理 MD 文件，不是 Ubuntu MVP 來源碼同步**；程式版本、實作版本、測試版本不得混報。

## 已在 Ubuntu 本機實施的最小修正
1. `app/Actions/Forms/FormSubmissions.php`：`publish()` 的 transaction closure 補 `use ($open)`；備份 `FormSubmissions.php.bak_20261101_213529`；lint 通過。
2. `tests/Feature/BatchFTest.php`：11 處 `DB::table(...)->whereKey(...)` 改 `where('id', ...)`（Query Builder 原先誤查 `key`）；lint 通過。
3. `tests/Unit/PageAtlasManifestValidatorTest.php`：repo root 修改為 `/project`；已有備份。
4. `tests/Schema/BillingConstraintsTest.php`、`StructureTest.php`：增加 webhook SHA fixture，修正 MySQL `EXTRA` 大小寫與外鍵檢查範圍；Schema 測試通過。
5. 曾套用 `Shared_Modules_V1_Static_Audit_Fix02.zip`；實際 git 狀態、所有修改檔案須以 Ubuntu 現況重新確認。**未提交本機源碼到此 GitHub**。

## BatchF 最新 11 個失敗（依 2026-11-01 最後一次測試）
| 類型 | 次數 | 現象與調查指引 |
|---|---:|---|
| ATTEND-01、F02 | 3 | `CreateBooking.php:119` 拒絕「報名已截止」。測試在已開始的活動上建立報名；應修正 fixture／前置條件，**不可放寬正式截止規則**。 |
| NOTIFY-02 | 1 | 預期送 2 個推播裝置，實際 0。檢查 `user.is_active`、`is_family_user`、PushPreferences、DeviceToken 及模組 gate。 |
| F03、R05 | 2 | Push lease takeover／裝置傳送不符預期，可能同受用戶狀態、偏好、token 或 claim 邏輯影響。先找共同原因，不可直接刪掉測試。 |
| SHOP-01、F01×2、R01 | 4 | `ModuleRegistry.php:160` 拒絕 MODULE_DISABLED。核對模組 state、dependency、required config 和測試 fixture；勿繞過 gate。 |
| R03/R04 | 1 | CSV `FormResource::exportCsv()` 把未登入時 `auth()->id()` 強制轉成 0，使 `audit_events.actor_id` FK 失敗；應確認要求管理員授權與稽核行為，不可關 FK。 |

## 完整 Feature 舊 46 Fail 其他待查
- Page Atlas：`mobile/lib/app.dart` 相對路徑不存在、同步錯誤訊息與 lock 情境；與 `/project` 根目錄核對。
- Catalog：ServiceSlotTemplate `currency` 可能為 NULL，使 `calendar_events.currency` 違反 NOT NULL；`CreateSeriesWithLessons` validator、單場 scope 錯誤訊息。
- Content：public post visibility、comment open/blacklist、favorite placeholder。
- BookingFlow：`DueSource::Promotion` enum casting 與測試字串預期。
- Media：18,874,369 bytes 上限，檢查實際驗證訊息。
- GProfiles：Laravel container `config` binding/測試 bootstrap。
- ModuleSwitch：依賴模組仍啟用時不得直接停用其前置模組。
- 以重跑完整 Feature 的最新結果為準，不能推斷 11 個 BatchF 之外已成功。

## 開發要求
- 以 Ubuntu **當前** MVP 程式為唯一修改基線，保留舊 PoC，不直接解壓覆蓋。
- 修復前備份、一次集中針對共同原因修改、PHP lint、受影響 Pest、全套回歸；不裝不必要套件。
- 保持 TestDatabaseGuard、合法外鍵、模組依賴、報名截止、付款和權限規則。
- 不操作正式資料、不得觸碰 iStudy、不自動合併 PR、不建立零碎 commits。
- GitHub 文件依 `AGENTS.md` 的「需求／實作／驗收」三版本分離。

## 查閱已知錯誤（2026-11-01 增補）
- Debug 前先讀 **[已知錯誤、根因、防止重犯](../testing/KNOWN_ISSUES_AND_PREVENTION.md)**，同一個錯誤不得每次對話重新摸索；已修項目要先查本機現況再決定是否重做。
- 此交接記錄來自使用者上傳 TXT 的**時間線**：舊 PoC 掛載、尚未安裝 vendor、Migration 中途失敗等都是**歷史狀態**，已由後來 25/25 migration、Pest 實測取代。絕不可因讀到舊段落而重裝重建。
- 新 AI 第一輪**只需回報對專案目標、正式決策、進度、環境和已知錯誤的理解**，禁止直接改碼。入口見 [新對話接手](NEW_CHAT_UBUNTU_MVP.md)。


## 2026-11-01 21:39（台灣時間）｜BatchF 實機回測原始證據補錄

**證據來源：** 使用者提供的終端輸出 `貼上的文字 (1)(20261101-133907).txt`（記錄時間約 UTC 13:39 = 台灣 21:39；實際測試輸出 15.16 秒）。這份證據是執行 `DB::table(...)->whereKey(...)` 修復後的**第一手回測**，不能只留下推測性的摘要。以下為原始輸出逐項整理，未聲稱重新跑過測試。

### 本次執行及修改確認
- Python 修正輸出：`FIXED tests/Feature/BatchFTest.php: 11 occurrences`、`TOTAL: 11`。
- PHP lint：`No syntax errors detected in tests/Feature/BatchFTest.php`。
- 測試執行：
```bash
docker exec -w /workspace \
  -e APP_ENV=testing \
  -e DB_CONNECTION=mysql \
  -e DB_DATABASE=shared_pest_test \
  -e DB_TEST_EXPECTED_DATABASE=shared_pest_test \
  eachcore-dev-php-1 \
  ./vendor/bin/pest tests/Feature/BatchFTest.php --colors=never --compact
```
- 結果：**20 passed，11 failed，104 assertions；Duration 15.16s**。
- 原先 `Unknown column 'key' in 'where clause'` 的失敗未再出現於這次 11 個失敗中。**這證明本次 BatchF 的 SQL key 類失敗已消除，但不是全 repo 已全面掃描或 Feature 全套 PASS。**
- Query Builder 與 Eloquent 區分：`DB::table(...)->whereKey(...)` 造成 SQL `where key=N`，應在確認表主鍵為 `id` 後改為 `->where('id', $id)`；Eloquent `Model::query()->whereKey()` 本身合法，**不得一律替換**。

### 11 個 FAIL 逐項核對（完整數量）
| # | 原始測試名稱／位置 | 實際失敗 | 下一步調查 |
|---|---|---|---|
| 1 | `ATTEND-01: location Q…` | `CreateBooking.php:119`：報名已截止 | 檢查測試報名時間、活動起始時間 |
| 2 | `ATTEND-01: staff scan…` | `CreateBooking.php:119`：報名已截止 | 同上；不得繞過截止規則 |
| 3 | `NOTIFY-02: one push per notificatio…`，`BatchFTest.php:279` | `count($sent)` 期望 2、實際 0 | 檢查推播偏好、裝置、使用者狀態、模組設定 |
| 4 | `SHOP-01: Woo products…` | `ModuleRegistry.php:160`：此功能目前未開放 | 查 SHOP-01 模組狀態及依賴 |
| 5 | `F01: a sync that hits…` | `ModuleRegistry.php:160`：此功能目前未開放 | 查模組狀態／fixture |
| 6 | `F01: a short last pag…` | `ModuleRegistry.php:160`：此功能目前未開放 | 同上 |
| 7 | `F02: GPS sessions fai…` | `CreateBooking.php:119`：報名已截止 | 檢查測試前置時間 |
| 8 | `F03: a live claim blocks a second w…`，`BatchFTest.php:385` | 預期 `RuntimeException`，但未拋出 | 檢查 stale claim、發送流程與 fixture |
| 9 | `R01: an exact multipl…` | `ModuleRegistry.php:160`：此功能目前未開放 | 查模組依賴／必要設定 |
| 10 | `R05: a worker whose lease was taken…`，`BatchFTest.php:499` | 期望 `['tok-1']`，實際 `[]` | 檢查發送前 gate、claim 和 token |
| 11 | `R03 / R04: CSV keep…`，`FormResource.php:202`、`Audit.php:30` | `audit_events.actor_id=0`，MySQL FK 1452 | 查 `auth()->id()` 是否為 null、管理員認證與稽核規則 |

### 本次不能宣稱的事情
- 這份 TXT **沒有**提供全部 Feature 的新回歸結果；原先完整 Feature 114 PASS／46 FAIL 仍是歷史結果。
- 11 個 FAIL 的共同根因尚未證實；推播、模組 gate 和時間 fixture 是**排查方向**，不是已完成修正。
- 沒有證據顯示 Concurrency、Flutter Analyze/Test 或 Android 建置已通過。
- **不要再次執行同一批 whereKey 修補**，除非本機目前原始碼證明該錯誤重現；先查 [已知錯誤與防止重犯](../testing/KNOWN_ISSUES_AND_PREVENTION.md)。


## 2026-11-02｜最新實機回報與加速驗收順序
- 使用者在既有 PHP 容器 `eachcore-dev-php-1` 執行 `php -l tests/Feature/BatchFTest.php`，回傳 `No syntax errors detected in tests/Feature/BatchFTest.php`，記為 **PHP lint PASS（本輪）**；**不是** BatchF Pest PASS，也沒有新 Feature／Concurrency／Flutter 結果。
- 依既有 Pest 安全規範，後續優先單次收集 BatchF 與完整 Feature 最新測試輸出，分類根因後集中修復，再執行 Unit／Schema／Feature／Concurrency／Flutter／Android 的分層驗收；Concurrency 前必須確認 TestDatabaseGuard 攔截錯誤 DB。
- 避免再問已知 Ubuntu 路徑、主機 PHP、容器名稱、DB 分工；需要最新證據時只提供一段可複製的 Docker 指令。保留 11 FAIL 歷史狀態直到有新 Pest 結果取代。

## 2026-11-02 01:49–01:50（台灣）｜最新 Ubuntu 實機回歸
- 來源：使用者上傳終端輸出 `貼上的文字 (1)(20261101-175204).txt`；**本次僅分析日誌，未修改 Ubuntu 原始碼**。
- Docker mount 確認：MVP ZIP root `.../Shared_Modules_V1 -> /project`；backend `.../Shared_Modules_V1/backend -> /workspace`。
- `.env.testing`：`APP_ENV=testing`、`DB_CONNECTION=mysql`、`DB_DATABASE=shared_pest_test`、`DB_TEST_EXPECTED_DATABASE=shared_pest_test`。
- BatchF：**20 PASS／11 FAIL，104 assertions，16.61s**；失敗分布與上一輪相同：報名截止3、Push3、SHOP/module gate4、Form CSV Audit1。
- 完整 Feature：**124 PASS／36 FAIL，502 assertions，63.14s**；相較舊輪 114 PASS／46 FAIL，淨改善10。不能稱整套 PASS。`BatchEPointsCalendarPaymentTest`、`MediaCenterTest`、`InvariantCheckerTest`、`LockScopeTest`、`ReviewFixesTest`、`TransactionRunnerTest` 等檔案在此次 Feature 輸出為 PASS。
- Feature 額外失敗包括：Page Atlas（來源路徑 `mobile/lib/app.dart` 不存在、鎖/回滾/同步）、ACCESS posts visibility、Catalog 單場 reschedule 斷言與 service currency NULL、Content 留言/收藏、Booking `DueSource` enum 與字串比較、CatalogAccess 建立課程 fixture 驗證、Media 18MiB 邊界、GProfiles、ModuleSwitch。詳情依原始日誌；尚未逐項確認根因。
- **下一步**：先讀取目前 Ubuntu 原始碼相關段落，按共同根因分組制定一次性修補；不得直接取消 gate、截止時間、FK 或 TestDatabaseGuard。修復後先目標測試再完整回歸；Concurrency 需另驗 Guard 後才可跑。

## 2026-11-02｜213 PHP 原始碼＋Feature 日誌集中 Debug 靜態審查
- 使用者提供 `shared-mvp-diagnostic-20261102(1).zip`（213 PHP 檔）與 `feature_20261102.log`；**Feature 124 PASS／36 FAIL，502 assertions**。已作跨層靜態審查，**未修改 Ubuntu 程式／未重跑測試**。
- 分類：Page Atlas 來源驗證／交易測試；CatalogAccess `makeSeries(price=0)` 與 `CreateSeriesWithLessons` `price min:1`；BatchF `bfLiveEvent()` 已開始後仍建立 booking；Push lease、preferences、module gate；SHOP sync module gate；CSV `FormResource::exportCsv()` `(int) auth()->id()` 導致 actor_id=0 FK；ServiceSlot currency NULL；Content comments_enabled；Favorites placeholder；BookingFlow DueSource enum/string；ModuleSwitch TAG dependency；GProfiles dataset config boot；Media 18MiB error order；posts visibility；Reschedule error order。
- 特別注意：上述部分為根因候選，需依缺失的 `routes/api.php`、Controllers、config、migrations、factories、Flutter 與實機驗證，不可逕稱全部已修。嚴禁為讓測試變綠而放寬 booking 截止、模組依賴、ACCESS、外鍵、Page Atlas source check、Push lease 或 DB Guard。
- 下一輪優先**一次取得缺失的整套原始碼**，按跨層 API→Controller→Action→Model→DB→Response 和測試 fixtures 集中修正；目標回歸後完整 Pest/Concurrency/Flutter/Android 驗收。已有環境資訊勿要求使用者重查。

### 2026-11-02｜418-file fullchain ZIP 與首批集中修補
- 收到 `shared-mvp-fullchain-20261102.zip`，ZIP 419 entries，418 候選來源；已解壓分析。內有 backend routes 3、migrations 25，但 **flutter/ 0、backend/config/ 0**，故仍不能稱 Flutter/config 全鏈路已覆蓋。
- 產出 `shared_mvp_debug_safe_patch_20261102.patch`（7 files）及同名 ZIP：`GenerateServiceSlots` currency null fallback TWD；`FormResource::exportCsv` 未登入 audit actor_id 保留 null 避免 FK 0；`CatalogAccessTest` makeSeries price 0→1；`BatchCContentGrowthTest` bcPost comments_enabled=true；`BookingFlowTest` DueSource enum 比對；`GProfilesTest` dataset 在 Laravel bootstrap 前不呼叫 config()；`BatchBCatalogTest` service template currency TWD。
- 靜態 patch apply check PASS；**未執行 PHP/Pest、未修改 Ubuntu 原始碼、未宣稱修復 36 FAIL**。需用 `shared_pest_test` 驗證，並繼續處理 PageAtlas source fixture、ATTEND live booking setup、Push、SHOP gate、module dependencies、Favorites pagination 等。

### 2026-11-02｜Page Atlas 第二批修補（未實測）
- 產出 `shared_mvp_debug_followup_20261102.patch` 與 ZIP，3 files：`tests/Feature/PageAtlasTest.php`、`tests/Feature/Batch4ReviewFixesTest.php` 測試專用 `config(['page_atlas.check_sources'=>false])`，不修改正式來源檢查；`app/Services/PageAtlas/PageAtlasSync.php` 在驗證成功且空 manifest 時直接回傳，避免無效等待同步鎖。
- 已靜態 `git apply --check` 無錯；尚未套用 Ubuntu、尚未跑 Pest。與前一批 7 files 合計 10 files；**Feature baseline 124 PASS/36 FAIL** 不變。需注意 source path 檢查本身應另有專用測試，不能因 synthetic fixture 變更而失去覆蓋。

### 2026-11-02｜第三批 ATTEND-01 測試 fixture 修補（未實測）
- 根因已由原始碼確認：`BatchFTest.php` 的 `bfLiveEvent()` 建立開始於 `now()-10min` 的活動，之後才呼叫 `CreateBooking`，被 `CreateBooking` 的 `hasStarted()` 正確拒絕。
- 產出 `shared_mvp_attendance_fixture_20261102.patch` / ZIP：只改 `backend/tests/Feature/BatchFTest.php`，新增 `bfBookForCheckin()` 先移到未來合法報名，再移回目前簽到窗口；兩處 ATTEND-01 測試使用此 helper。**未放寬正式報名截止邏輯**。
- `git apply --check` PASS；未套用 Ubuntu、未跑 Pest。先前兩批 patch 也仍未實測，Feature baseline 124 PASS/36 FAIL。

### 2026-11-02｜三批合併為單一整合 Patch
- 產出 `shared_mvp_debug_consolidated_20261102.patch` 與 ZIP，涵蓋 11 個不同檔案（前批次重疊檔案合併計算）。在上傳的 418-file snapshot 上 `git apply --check` PASS；ZIP 含原始檔快照供核對。
- 深入檢視 `SendPushNotification::handle`：先 claim、偏好/帳號過濾、逐裝置 renew lease、remember、finish，具防重送與失效裝置處理；仍須以實機測試釐清 0 sends，不能任意移除 lease 或權限檢查。
- `Audit::record` 接受 nullable actor，Migration `2026_11_01_000008_create_notifications_settings_audit.php` 的 audit_events.actor_id nullable FK；故 CSV audit 的 0→null 修補符合既有 schema，但權限端點仍須另行驗證。
- **尚未套用 Ubuntu／未執行 PHP/Pest**，baseline 124 PASS/36 FAIL。下階段仍須實機回歸，並處理 SHOP module requires/dependencies、Content module dependencies、PageAtlas source/manifest、Push fixture 等剩餘根因。

### 2026-11-02 02:15｜Ubuntu 整合 Patch 實機 Feature 回歸
- 使用者確認 `git apply --reverse --check` 顯示 `PATCH_ALREADY_APPLIED`；排除 Docker storage/vendor/env 的原始碼備份 `mvp-source-20261102-021507.tar.gz` 建立成功。
- 上傳 `mvp-debug-result-20261102-021553.zip`，含 syntax + feature log；11 個變更 PHP 檔 `php -l` 全部 PASS。
- 完整 Feature：**145 passed, 17 failed, 620 assertions, 60.19s**；前次 **124 passed, 36 failed, 502 assertions**，失敗淨減 19。注意測試總數從 160 增至 162，需比較個別案例而非宣稱 19 項皆已修復。
- 剩餘失敗：BatchAPlatform 1、BatchBCatalog 1、BatchCContentGrowth 1、BatchF 7、Decision1031 1、ModuleSwitch 3、PageAtlas 2（合計 17）。
- PageAtlas：retire-only 測試中未退休的 App-Tst-02 status 變 `unverified` 而非 `active`；batch midway DB CHECK constraint throws 後 App-Tst-01 page_name 仍為 `新名` 而非原名，疑似交易隔離／表引擎／測試交易連線，需檢查 Migration 與 MySQL 行為，不可放寬交易原子性。
- 尚未完成 17 FAIL 的修復，需針對實機日誌與原始碼逐項分析後產出下一批補丁；不碰正式 DB。

### 2026-11-02｜第四批增量 Patch（尚未 Ubuntu 實測）
- `shared_mvp_debug_round4_20261102.patch`：僅 2 個測試檔，針對**已套用前三批整合補丁的工作樹**，不是原始 ZIP；在該基線上反向 `git apply --reverse --check` 成功。
- `BatchFTest.php` F02：原測試對已開始的 `bfLiveEvent()` 直接 `CreateBooking`，合法觸發 BOOKING_CLOSED；改用現有 `bfBookForCheckin()` fixture，先預約後移至簽到時段，不削弱正式規則。
- `PageAtlasTest.php` retire-only：`paDef()` 的元件及 DB refs 沒有 verified 標示，`ManifestValidator` 按規則產出 `unverified`，故未退休的第二頁應保持 `unverified`；修正錯誤預期，保留證據檢查。
- **重要未解**：PageAtlas `DB::transaction` 中第二筆 CHECK 失敗後第一筆修改仍保留；必須檢查 MySQL 表引擎、交易連線及測試中的 DDL 隱含 commit。不得修改成允許部分寫入或跳過原子性測試。
- 此批未經 Ubuntu PHP/Pest 回歸；不宣稱剩餘 17 項已減少。

### 2026-11-02｜第五批增量 Patch（尚未 Ubuntu 實測）
- `shared_mvp_debug_round5_20261102.patch`，以**已套用第四批的原始碼**為基線，修改 `backend/tests/Feature/BatchFTest.php`、`backend/tests/Feature/ModuleSwitchTest.php`；本地 `git apply --check` PASS。
- SHOP 四個測試：引入 `bfShopReady()`，保留 `bfModule('shop','enabled')` 並設定測試 `modules.requirements.shop='configured'`；參照 GProfilesTest 已使用的同一部署要求鍵。正式 `ModuleRegistry::requirementMet` 不變，仍須 Ubuntu 驗證是否另有依賴條件。
- ModuleSwitch 三個測試：引入 `disableContentDependents()`，使用 `ModuleRegistry::dependents('content', true)` 列出啟用中的直接依賴者，先經 `requestDisable` 再停用 content；不削弱生產的模組依賴安全。若存在多層依賴仍須實測檢查。
- 第四批修正 F02 booking fixture 與 PageAtlas 未驗證狀態，尚未 Ubuntu 實測；PageAtlas batch rollback 原子性問題未修。
- **基準仍為 145 PASS / 17 FAIL**，不能視為新批次通過。

### 2026-11-02｜第六批增量 Patch：PageAtlas 回滾測試根因
- `shared_mvp_debug_round6_20261102.patch`，基線為第四＋第五批均套用後的原始碼，僅修改 `backend/tests/Feature/PageAtlasTest.php`，本地反向 `git apply --reverse --check` PASS。
- 根因：測試於 Laravel `RefreshDatabase` 的交易內執行 `ALTER TABLE ui_pages ADD CONSTRAINT`，MySQL DDL 隱含提交，破壞測試交易邊界，導致第一筆更新沒有回滾；不能據此認定正式 `PageAtlasSync` 的 `DB::transaction` 失效。
- 改用隔離的 Eloquent model event dispatcher，在第二筆 `UiPage::saving` 時拋出例外；保留第一筆更新後第二筆失敗的完整 rollback 斷言；`finally` 恢復原 dispatcher，不動 schema、不改 production 程式。
- 尚未 Ubuntu 實測；仍需 Pest 回歸驗證；基準 145 PASS/17 FAIL。

### 2026-11-02｜第七批增量 Patch（靜態檢查通過，待 Ubuntu 回歸）
- `shared_mvp_debug_round7_20261102.patch`：以已套用前三批整合版＋第四至第六批為基線；僅改 `BatchCContentGrowthTest.php` 與 `BatchBCatalogTest.php`，本地 `git apply --check` PASS。
- 收藏：`Favorites::list()` 以 id DESC，每頁 50 筆；1,000 筆收藏中第一筆位於第 20 頁。原測試只找 page 1，`firstWhere` 回傳 null；修正測試為 page 20，不改正式分頁或 1,000 上限。
- 單場改期：`RescheduleSession::handle` 對 scope=all 呼叫 `ApiException::validation(['scope'=>['單場活動只能改這一場。']])`，頂層 message 是 `輸入資料有誤。`；改測試檢查 `$e->fields['scope']`，保留欄位規則，不修改 production。
- 仍未套用 Ubuntu／執行 Pest；實測基準 **145 PASS/17 FAIL**。第七批必須在第六批之後套用，勿對目前只有前三批的 Ubuntu 工作樹直接套用。

### 2026-11-02｜第八批增量 Patch：媒體 18 MiB 上限斷言
- `shared_mvp_debug_round8_20261102.patch`，基線為已套用第四至第七批的原始碼，僅改 `backend/tests/Feature/Decision1031Test.php`；本地 `git apply --check` PASS。
- `MediaStorageService::readAndValidate` 先判斷 filesize 大於 18,874,368 bytes，使用 `ApiException::validation(['file'=>['...18 MiB...']])` 拒絕，頂層例外訊息固定為「輸入資料有誤。」。原測試 `toThrow(ApiException::class, '18 MiB')` 因訊息位置錯誤失敗。
- 改為 try/catch 檢查 `$e->status === 422` 與 `implode(' ', $e->fields['file'] ?? [])` 包含 `18 MiB`；保留無副作用斷言及恰好上限可接受的驗證；正式媒體程式不變。
- 尚未 Ubuntu 實測；基準仍為 145 PASS / 17 FAIL。下一步查 Push/ACCESS-01，並整合第 4–8 批成單一 Ubuntu patch，避免重複套用。

### 2026-11-02｜第四至第八批合併補丁與 Push 追查
- 已產出 `shared_mvp_debug_round4to8_merged_20261102.zip`（含同名 `.patch`）；基線為 Ubuntu 已套用前三批整合版，涵蓋 4–8 批。從 round8 檔案樹反向撤銷 8→4，再以檔案差異建立單一 patch；對未套用 4–8 的基線 `git apply --check` PASS。
- Push 失敗 NOTIFY-02/F03/R05 均有 `handle()` 未呼叫 sender/未拋預期錯誤現象；初查 `SendPushNotification::handle` 於 claim 後會因 User active/family 或 PushPreferences 禁止而直接 finish；UserFactory verified() 明確設 active=true/family=false，故不宜猜測改掉權限。需檢查實際 `push.categories` / `push.template_categories`、notification model cast/DB 值及測試設定，勿破壞 lease/retry 語義。
- ACCESS-01 的 BatchAPlatform 測試預期 guest 列表包含 public post，但實測為空；需釐清頁面 access rule/帖子條件與 fixture，未修改生產存取策略。
- **尚未 Ubuntu 回歸第四至第八批**，基準仍 145 PASS/17 FAIL；待確認其餘 Push/ACCESS 問題後再做下一輪整合與實測。

### 2026-11-02 02:27｜Round4–8 Ubuntu 回歸中斷（PHP 128 MiB OOM）
- 使用者上傳 `mvp-debug-round4to8-result-20261102-022715.zip`；6 個修改 PHP 測試檔 `php -l` 全部 PASS。
- 已執行的 BatchBCatalog、BatchCContentGrowth 全部 PASS；BatchF ATTEND/F02 PASS，NOTIFY-02、SHOP-01、F03、R05 仍 FAIL；BatchAPlatform ACCESS-01 仍 FAIL。
- **完整 Pest 未跑完**：在 `app/Services/Media/MediaKeyring.php:122` `openssl_encrypt`，PHP Fatal `Allowed memory size of 134217728 bytes exhausted (tried to allocate 18878464 bytes)`；不得把此輪當成完整 PASS/FAIL 統計，也不能認定媒體加密程式失效。
- 下一步僅重跑完整 Feature，`php -d memory_limit=512M ./vendor/bin/pest tests/Feature --colors=never`，仍使用 `APP_ENV=testing DB_DATABASE=shared_pest_test DB_TEST_EXPECTED_DATABASE=shared_pest_test`，不修改正式 php.ini 或 DB。取得完整日誌再排查 Push/SHOP/ACCESS/ModuleSwitch/PageAtlas。

### 2026-11-02 02:28｜512MiB 完整回歸結果：157 PASS / 5 FAIL
- 實機 `feature_512m_20261102-022850.log`：**157 passed, 5 failed, 654 assertions, 49.40s**，無 PHP OOM。前次完整 145/17，最初 124/36；總失敗由 36 減至 5。
- 剩餘五項：`BatchAPlatformTest` ACCESS-01 訪客 public post list 期望 `[id]` 實際 `[]`（line 115）；`BatchFTest` NOTIFY-02 send count 0 vs 2（line 303）；SHOP-01 `sync()` seen 0 vs 2（line 323）；F03 stale claim 未拋 RuntimeException（line 409）；R05 lease takeover token log 空 vs tok-1（line 523）。
- ModuleSwitch、PageAtlas（含 batch rollback）、BatchB、BatchC、Decision1031（18MiB）、F02 全部 PASS。
- SHOP 根因線索：`WooCatalogSync::readAll()` 對缺少 X-WP-Total headers 的短頁不視為完成，須再讀到空頁確認；SHOP-01 `Http::sequence()` 僅提供兩個非空 response，可能在第三頁失敗而使 `sync()` 回報 seen=0；應與 F01 已通過的測試對照，不可放寬正式分頁一致性。
- Push 三項均無 sender 觸發，優先檢查 `PushPreferences::allows`、通知 `claim` 早退、User active/family；不刪除 lease 保護。ACCESS 需檢查 seed 的 page rule、Post visibility 與列表查詢。
- 尚未產出第九批修補；須先定位根因再修復，不能宣稱 5 項已通過。

### 2026-11-02｜第九批：ACCESS/SHOP fixture 根因＋Push 前置診斷
- 產出 `shared_mvp_debug_round9_20261102.patch` 與 ZIP，基線為 Ubuntu 已套用第 1–8 批後的工作樹，僅修改 `backend/tests/Feature/BatchAPlatformTest.php` 和 `backend/tests/Feature/BatchFTest.php`；`git apply --check` 和 `git apply --reverse --check` 在相應本地基線均 PASS。
- ACCESS-01：測試新建 Post 未設定 status，migration `2026_11_03_000016` 的預設是 `draft`，`Post::scopePublished` 正確拒絕草稿；兩篇 fixture 加 `'status'=>'published'`，不修改 API/存取規則。
- SHOP-01：`WooCatalogSync::readAll` 在沒有分頁標頭時不能以短頁證明完整，第二次讀取會耗盡 fake sequence 導致 `seen=0`；兩次模擬回應補上各自一致的 `X-WP-Total` 和 `X-WP-TotalPages`，不放寬正式同步的分頁驗證。
- Push：NOTIFY-02 加入執行前條件斷言（active、非 family、read_at null、preferences allow、兩筆有效 token）以定位早退根因；F03/R05 仍需同類根因追查，不改正式 claim/lease/renew 安全機制。
- **尚未 Ubuntu 實測**，基準仍 **157 PASS/5 FAIL**。本批需套用在第 1–8 批已套用後的版本，勿重套前三批。

### 2026-11-02｜第九批增量修補（5 FAIL 的安全修正與 Push 診斷）
- 產出 `shared_mvp_debug_round9_20261102.zip`（內含 `.patch`），基線為 Ubuntu 已套用前 3 批＋4–8 合併補丁的工作樹。本地在 round8 基線 `git apply --check` PASS。僅改 `BatchAPlatformTest.php`、`BatchFTest.php`，不修改正式程式。
- ACCESS-01：`Post::scopePublished` 必須 `status` 為 `scheduled/published` 且 `published_at<=now`；原測試只給 published_at，資料表預設 draft。兩筆測試文章加 `status='published'`，維持公開／會員 ACL 驗證。
- SHOP-01：原測試 `Http::sequence` 兩次均無分頁 header；同步程序要求完成證據，回應耗盡可能導致 seen=0。補 `X-WP-Total`、`X-WP-TotalPages` 與每次 sync 的實際商品數一致，保留 F01 防不完整分頁取消發布規則。
- Push：NOTIFY-02 在 `handle` 前新增 `is_active=true`、`is_family_user=false`、`read_at=null`、`PushPreferences::allows=true`、有效裝置數=2 的斷言，協助找出提前結束的共同根因；F03/R05 尚未直接修改，仍可能失敗。不得將此補丁宣稱為 Push 修復。
- **尚未 Ubuntu 實測**，最新完整基準 157 PASS / 5 FAIL。請 Ubuntu 先做 `git apply --check`、備份、套用、執行 `php -d memory_limit=512M ./vendor/bin/pest tests/Feature --colors=never`，上傳 log，再決定 Push 正式修正。
