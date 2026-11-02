# Shared Modules V1 MVP｜Debug 根因索引與防重犯手冊（2026-11-02）

> 用途：給後續 ChatGPT、Claude 與開發人員快速查找已知問題。**這是實測排錯知識，不是新業務規格**。先讀 `AGENTS.md`、`PROJECT.md`、`docs/testing/KNOWN_ISSUES_AND_PREVENTION.md`、`docs/handoff/ubuntu_mvp_debug_20261101.md`。不可因 Feature 全綠自動結案。

## 最終實測基準
- 原始完整 Feature：124 PASS／36 FAIL（502 assertions），2026-11-02。
- 前三批修補後：145 PASS／17 FAIL（620 assertions），60.19 秒。
- 第四至第八批合併後第一次測試：PHP CLI 128 MiB 記憶體耗盡，**沒有完整結果**；不能拿中途 PASS 當總驗收。
- 改用 `php -d memory_limit=512M`：157 PASS／5 FAIL（654 assertions），49.40 秒。
- 第九批針對性：38 PASS／3 FAIL（204 assertions），18.57 秒；ACCESS-01 與 SHOP-01 已通過。
- 第十批 Push 診斷：三個 Push 仍 FAIL；診斷顯示 claim 成功、會員與偏好合格、有效裝置 2、發送器 0 次。
- 第十一批 Push 針對性：3 PASS／0 FAIL（21 assertions），12.23 秒。
- **第十一批後完整 Feature：162 PASS／0 FAIL（691 assertions），46.70 秒**，證據檔 `mvp-feature-final-round11-20261102-024503.zip`。這是 Ubuntu Docker 後端 Feature 驗證，不是 Flutter／外部服務／併發／正式部署驗收。

## 快速索引（症狀 → 根因 → 正確做法）
| ID | 搜尋關鍵字／症狀 | 根因／正確處理 | 禁止捷徑 |
|---|---|---|---|
| DBG-01 | `DB::table()->whereKey()`、Unknown column key | Query Builder 沒有 Eloquent `whereKey` 語意；依表主鍵使用 `where('id', ...)`。舊 BatchF 共修正 11 處。 | **不可**批次改 `Model::query()->whereKey()`，它是合法 Eloquent。 |
| DBG-02 | `FormSubmissions` closure undefined `$open` | 交易 closure 需 `use ($open)`；保留交易。 | 不刪交易。 |
| DBG-03 | `audit_events.actor_id=0`、CSV FK | `(int) auth()->id()` 在無登入時轉成 0；明確檢查操作員身分，保持稽核與外鍵。 | 不用 0 假裝使用者、不關 FK。 |
| DBG-04 | `PageAtlas` source `mobile/lib/app.dart` missing | 測試使用虛構來源檔；**僅測試 fixture** 依情境處理來源存在性檢查。 | 不關閉正式來源驗證。 |
| DBG-05 | Page Atlas empty manifest lock | 驗證空 manifest 後可直接返回，避免無謂等待同步鎖。 | 不移除正常批次的鎖。 |
| DBG-06 | Page Atlas rollback、`ALTER TABLE` | MySQL DDL 隱含 COMMIT 破壞測試交易；改在第二筆模型儲存時注入例外，驗證整批回滾。 | 不因 DDL 測試失敗就判定正式交易無效。 |
| DBG-07 | Page Atlas `active` vs `unverified` | 元件／DB 來源未驗證時應是 `unverified`；修正 fixture 預期。 | 不偽造已驗證狀態。 |
| DBG-08 | `CreateBooking` 報名已截止、F02 簽到 | 測試先讓活動開始才建立預約；調整建立時序。 | 不放寬報名截止或活動開始規則。 |
| DBG-09 | ServiceSlot currency NOT NULL | 建立模板／產生時必須提供有效幣別，修正來源與 fixture。 | 不放寬 DB 約束。 |
| DBG-10 | `DueSource::Promotion` vs `promotion` | enum 物件和字串不能直接等同；測試依實際型別比較。 | 不任意更改正式 enum 契約。 |
| DBG-11 | comments blacklist fail | 測試文章先需 `comments_enabled`；才能真正測黑名單分支。 | 不移除留言開關驗證。 |
| DBG-12 | `ModuleRegistry` dependency | 先按相依關係停用下游，再停用 `content`；SHOP 還需滿足 `modules.requirements.shop` 測試部署條件。 | 不繞過模組依賴／正式部署門檻。 |
| DBG-13 | Woo sync `seen=0`、短頁 | 無 `X-WP-Total` 等分頁證據時，不能把短頁當作同步完成；測試 fake response 補一致的 total/total-pages，或按協定提供空尾頁。 | 不允許未完整同步就發布商品快照。 |
| DBG-14 | guest public post list `[]` | 測試新建 Post 未設 status，預設 `draft`；明確設定 `published`。 | 不讓草稿對訪客公開。 |
| DBG-15 | favorites 1000 / page 1 | 最新優先、每頁 50；最舊收藏在第 20 頁。 | 不修改正式排序來迎合錯誤測試。 |
| DBG-16 | `ApiException` message mismatch | 頂層訊息是一般驗證錯誤；具體原因在 `fields.scope`、`fields.file`；測試應檢查欄位錯誤。 | 不以模糊字串斷言替代業務規則。 |
| DBG-17 | Media 18 MiB | 限制 **18,874,368 bytes**；超限檢查 HTTP 422、`fields.file` 與無新增紀錄。 | 不增加媒體上限或跳過加密驗證。 |
| DBG-18 | `Allowed memory size 134217728`、`openssl_encrypt` | PHP CLI 128 MiB 在 18 MiB 測試時耗盡；單次 Pest 使用 `php -d memory_limit=512M`。 | 不判定主機 RAM 不足、不直接修改正式 php.ini。 |
| DBG-19 | Push 0 sent、F03、R05 | `SendPushNotification::renew()` 以 `UPDATE push_claimed_at=now() === 1` 判租約；MySQL 同秒值未變時 affected rows 可為 0，誤判租約丟失。Round11 在更新 0 筆時查詢相同 id+claim 是否仍存在；保留 claim 限制。 | 不刪 lease、claim、偏好、裝置安全檢查。 |
| DBG-20 | `TestDatabaseGuard refused` | 只傳 `APP_ENV=testing` 時 Docker 仍可能指向 `shared_mvp_test`；需同時顯式設定 DB_CONNECTION、DB_DATABASE、DB_TEST_EXPECTED_DATABASE。 | 不關閉 Guard，不對 `shared_mvp_test` 跑 Pest。 |
| DBG-21 | `php: command not found` | Ubuntu 主機無 PHP CLI；使用 `docker exec -w /workspace eachcore-dev-php-1 php ...`。 | 不為 lint 額外安裝另一套 PHP。 |
| DBG-22 | ZIP workspace not git repo | `Shared_Modules_V1` 是 ZIP 工作樹，`git apply` 可用但 `git status` 不適合作為必備檢查；用 `git apply --check`／`--reverse --check`。 | 不誤認為 Git clone 或覆蓋舊 PoC。 |

## Push 排查決策樹
1. 先確認通知 `pushed_at`、`read_at`、`push_claim`、`push_claimed_at`、`push_attempts`；只輸出布林值與計數，不輸出 token。
2. 檢查會員 `is_active`、`is_family_user`、`PushPreferences::allows`；查有效 `DeviceToken` 數及 `push.max_tokens_per_user`。
3. 若 `push_attempts` 不增加，先查 `claim()`；若增加、claim 存在但 sender 0 次，查 `renew()`／token 查詢；若 sender 有呼叫再查失敗 token、lease takeover、重試。
4. Ubuntu 實測 `push.max_tokens_per_user=10`，並非 0；`push.claim_ttl_seconds`、`push.max_attempts` 設定鍵為 null，**不能據此宣稱缺少必要配置**，需查 Job 常數與程式預設。
5. Round11 三項 Push targeted PASS，完整 Feature 162 PASS；但高併發 race 仍需專門測試，不能由此推論形式化無競態。

## AI 操作防重犯規則
- **先查本索引與現有 Ubuntu 基線**，不重跑已排除的假設，不反覆要求使用者回報已確認的路徑、版本與 DB。
- 依「原始日誌 → 原始碼 → 業務契約 → 根因 → 最小修補 → lint → targeted Pest → full Pest」閉環；根因未證實前只能標 `HYPOTHESIS`。
- 分清 `FIXTURE`、`PRODUCTION BUG`、`ENVIRONMENT`、`TEST DESIGN`；不以修改測試期待掩蓋真正產品缺陷。
- 修補前備份；Patch 僅在指定基線套用，先 `git apply --check`；已套用時 `git apply --reverse --check`，避免重複。
- 避免過多增量補丁，集中合併並完整回歸；不操作正式資料庫、不動 iStudy、不安裝無必要套件。
- 測試證據必須記錄 PASS、FAIL、assertions、秒數、測試範圍、來源檔名。**不以 CI 綠燈自動關閉缺陷**。
- 下一階段仍需 Unit、Schema、Concurrency、Flutter、外部 FCM/Woo、E2E、部署驗證；它們不包含在 162 項 Feature 之內。

## 2026-11-02 B1 PHP lint 防重犯：Docker 路徑及子 Shell 錯誤處理

- **BUG-UBUNTU-B1-LINT-01（已確認指令錯誤，程式未受損）**：`eachcore-dev-php-1` 的 `/workspace` 是 Laravel `backend/` 根目錄；錯誤指令使用 `/workspace/backend/app/...`，PHP 回 `Could not open input file`。正確容器路徑應為 `/workspace/app/...`、`/workspace/tests/...`，先確認 `docker exec -w /workspace eachcore-dev-php-1 pwd` 與 `test -f`。
- **BUG-UBUNTU-B1-LINT-02（已確認指令錯誤）**：在 `( ... )` 子 Shell 中直接使用 `return 1`，Bash 回 `return: can only 'return' from a function or sourced script`，迴圈繼續，最後顯示錯誤的 `結果代碼：0`。禁止此寫法。使用明確 `fail=0` 累積錯誤，`if ...; then ...; else fail=1; fi`，最後依 fail 輸出 PASS/FAIL；不要用最後一個 `echo` 的狀態當整批測試結果。
- 本次使用者終端回報四檔 PHP 均**未實際 lint**；`git diff --check` 沒有報錯；B1 四檔仍在工作樹。不得標示 B1 lint PASS，更不能推論 Pest PASS。
- 任何終端命令不得關閉使用者互動式 Shell，遵守根目錄 `AGENTS.md` 第 11 節。不要重複套用 B1 Patch、不要重設 Git、不碰 iStudy。
