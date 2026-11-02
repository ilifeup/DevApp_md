# Shared Modules V1 MVP｜ChatGPT 全面接手（2026-11-02）

> 使用者確認 Claude Code 額度用盡，先前交付的「B1 範圍與 47 模組進度完整盤點」指令**沒有執行**。改由 ChatGPT 主導審查、計畫、GitHub 文件與可取得原始碼的補丁；不得誤稱 Claude 已輸出報告或完成 B1。

## 現有已確認基線
- Ubuntu 本機 `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1`：分支 `master`，root commit `e20b294`，tag `round11-baseline`，561 個檔案；本機無 Git 遠端。
- B0 Git 基線完成，但不等於 47 模組完成。GitHub DevApp_md 只管理文件。
- Round11 Feature 162 PASS／0 FAIL、691 assertions（先前實機證據）；Unit／Schema 只有較早測試結果，Round11 後待重跑；Concurrency／Flutter／Android／E2E 未驗收。
- 正式規模 47 模組（P0 33／P1 10／P2 4）；三 App 共用程式但各自 DB、Filament。App-T 優先。

## B1 審查範圍（尚未核准程式修改）
- F1：影音離線授權撤銷、裝置解除、金鑰外洩時回應 `ok`、`revoked`、`rekey_required` 的正確性與撤銷測試。
- F3：忘記密碼寄信失敗仍回 200、`password/forgot` 路由測試缺口；需先釐清同步／非同步語意。
- F5：活動簽到／簽退／重複掃描，P1，是否納入 B1 尚待核准。
- F2、F4、F6–F9 僅盤點，不自動擴大 B1。Claude 截圖為部分畫面，**完整檔案清單／根因／測試證據尚未交付**。
- 不建立新分支、不做無關重構、不擴大功能、不改正式 API/DB 契約；需要重大變更先核准。集中修改、測試、提交。

## ChatGPT 接手工作方式
1. 先讀 `AGENTS.md`、`PROJECT.md`、正式模組規格、最新 ChangeLog、已知錯誤與環境文件；維持需求／實作／驗收三版本分離。
2. GitHub 文件可直接審查與同步；**Ubuntu 本機程式碼無法由 GitHub 文件庫取得**，需要使用者上傳本機基線 ZIP 或必要檔案，或在 Ubuntu 執行一次性唯讀打包指令。不可拿舊 PoC 代替。
3. 收到本機基線後，先盤點 47 模組及 B1 實際檔案/根因/風險，輸出完整可下載 UTF-8 MD 報告；使用者核准後才提供精確 patch、單次套用與受控測試步驟。
4. 不接觸 iStudy、不重啟 Docker、不改 Compose、不動非測試 DB；Pest 僅能使用 `shared_pest_runner`、`shared_pest_test`，必須保留 TestDatabaseGuard。
5. 不存取或上傳 `.env`、密碼、金鑰、私人檔案、`vendor`、`.git`、備份；維持 [B0 安全處理](UBUNTU_LOCAL_GIT_BASELINE_B0_20261102.md)。
6. 每次重要審查/修正/驗收，產出完整 MD 可下載檔，並同步 DevApp_md 交接與已知問題（僅在有證據時更新狀態）。禁止僅靠終端截圖交接。

## 未完成的下一個具體任務
取得 `e20b294` 本機程式快照（敏感檔排除）後，**只讀**完成 47 模組整體進度盤點與 B1 F1/F3/F5 精確修改範圍報告；使用者批准之前不修改程式。

## 2026-11-02 最新接手狀態（優先於上方歷史待辦）

- 已取得 B0、審查並套用 B1 F1/F3 四檔補丁於 Ubuntu 本機，PHP lint 四檔 PASS；targeted Feature **11 PASS／35 assertions／15.35s**，full Feature **165 PASS／701 assertions／49.12s**。本機 Commit 未建立，F5 不納入本輪。
- **不要再要求使用者重複查 Docker、mount、MySQL 帳號、資料庫、密碼或 TestDatabaseGuard**。先直接重用 `docs/testing/pest_database_safety.md` 的成功命令；只有環境變動或明確失敗時才做最小檢查。
- 本輪待辦：Git/交接收尾，之後依使用者要求在 Ubuntu 執行 reboot；未重開機前不得標記已完成。

## 2026-11-02 B1 本機 Git 最終收尾

- Ubuntu 本機 `master` 已成功建立 Commit **`e84ee1e`**，訊息：`fix(b1): harden offline license grace and password recovery`。
- Git 實際回報：**4 files changed, 44 insertions(+), 1 deletion(-)**；新增 `backend/tests/Feature/B1PasswordForgotTest.php`。此前因 Git `Author identity unknown` 暫停提交，已解決；**不要重新 git add／commit、重新套補丁或重跑已通過的 Feature**。
- B1 targeted Pest：**11 passed／35 assertions／15.35s**；完整 Feature：**165 passed／701 assertions／49.12s**；四檔 PHP lint PASS。
- 使用者回報 Ubuntu 已 reboot。**尚未確認本機 `git status --short` 是否完全乾淨**，也尚未推送程式碼至 GitHub（本機先前無 remote）；不得宣稱遠端已同步。
- 下一階段直接從 `e84ee1e` 接續，避免反覆環境盤查；Unit／Schema／Concurrency／Flutter／Android／E2E 仍需分別驗收。GitHub `DevApp_md` 是文件庫，不是本機程式碼庫。
