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

