# B1 F1／F3｜本機 Git 修補與 Feature 回歸（2026-11-02）

## 程式基線與交付
Ubuntu 本機 `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend`，先前 `master` Commit **`e84ee1e`**：`fix(b1): harden offline license grace and password recovery`；4 files changed、44 insertions、1 deletion；新增 `backend/tests/Feature/B1PasswordForgotTest.php`。當時無遠端 Push 證據，**不得聲稱 GitHub 程式庫已更新**。

## 驗證證據
B1 F1/F3 目標 Feature：11 PASS／35 assertions／15.35s；完整 Feature：165 PASS／701 assertions／49.12s；四個 PHP 檔案 lint PASS、`git diff --check` PASS。此為 B1 階段的歷史實機證據，不代表 Flutter、Android、E2E 已驗收。

## 修正範圍與防重犯
B1 主要針對離線授權寬限及忘記密碼／恢復流程加固；詳細實際程式行為應以本機 `e84ee1e` 的 Git Diff 與原始測試為準，不能僅依 Commit 訊息推定全部契約細節。不得重複套用 B1 補丁、重建環境、重新執行已驗證的同一批測試作為開發起點。

## 後續關聯
2026-11-02 本機後續 `264ff1f` 為獨立並發修正 Commit；兩者不可混為同一次修補。後續 Feature 165／Unit 43／Schema 91／Concurrency 10 PASS，詳見 `docs/architecture/BOOKING_CONCURRENCY_RESEARCH_20261102.md`。
