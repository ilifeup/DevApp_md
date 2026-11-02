# 2026-11-02｜ChatGPT B0 ZIP 審查結果與 B1 範圍

> 來源：使用者上傳的本機 Git archive `Shared_Modules_B0_Source.zip`，SHA-256 `c60c2e463b58bfc9100b6845eaa937f2cd12342d81f57135239cac49c50dffa8`。僅靜態審查，未在 Ubuntu 執行測試或修改原始碼。

## 基線
- 本機 `e20b294`、tag `round11-baseline`；ZIP 內 561 個檔案：backend 455、mobile 82、docs 9、其他 15。
- 正式 47 模組（P0 33、P1 10、P2 4）。既有 `docs/47模組詳細進度_Ubuntu驗收_20261101.md` 含逐模組原始碼線索與待驗證項目；靜態存在不代表驗收通過。
- Round11 Feature 162 PASS、691 assertions 是較早實測證據；本次未重跑。Unit/Schema Round11 後待回歸，Concurrency、Flutter、Android、E2E 待驗。

## B1 精確檔案與判斷
- **F1**：`backend/app/Actions/Media/OfflineLicenses.php` 的 `check()` / `trackStatus()`，`backend/app/Actions/Media/MediaDevices.php` 的 `revoke()`，`backend/tests/Feature/MediaCenterTest.php`，`mobile/lib/media/offline_media.dart`。現有 revoked、expired、rekey_required 判斷與撤銷測試；先驗證未知金鑰狀態、輪替與撤銷邊界，不可先改語意。
- **F3**：`backend/routes/api.php:37`、`backend/app/Http/Controllers/Api/AuthController.php:61-75`、`backend/app/Services/OtpService.php:22-55`。忘記密碼 API 對帳號存在性採中性回應；Controller 僅捕捉 `ApiException`，Mail 實際送達不等於 HTTP 200。應補帳號枚舉、頻率限制、寄信例外及 OTP 一致性測試，先釐清規格，不直接新增 Queue/Outbox。
- **F5**：`backend/app/Actions/Attendance/CheckIn.php`、`backend/app/Http/Controllers/Api/CheckinController.php`、`backend/app/Services/CheckinToken.php`。已有交易、鎖、重複簽到/簽退處理；P1 暫不納入 B1 程式修改，待核准。
- F2、F4、F6–F9 缺本次 Claude B1 定義全文；不能將歷史 F01–F12 誤當本次 F1–F9。

## 決策與限制
1. 建議 B1 先限 F1、F3；F5 僅盤點與測試方案。
2. 先補失敗情境測試，確認根因後才集中最小修正。
3. 不新增功能、分支、無關重構，不修改正式 API/DB 契約；不得碰 iStudy、Docker Compose、全域 MySQL、非測試 DB。
4. Pest 僅能在核准後使用 `shared_pest_runner`、`shared_pest_test`，保留 TestDatabaseGuard；Concurrency 環境未驗證前不得執行。
5. 使用者核准前不修改本機程式；完成後集中回歸、單次提議本機 Commit，不自動推送 GitHub。
6. 完整本次審查報告已在 ChatGPT 對話產出 UTF-8 Markdown 檔 `Shared_Modules_B0_47Modules_B1_Review_20261102.md`，該檔**未自動上傳 GitHub**；本文件為 GitHub 同步摘要。
