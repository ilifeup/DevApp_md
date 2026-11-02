# Shared Modules MVP｜B1 F1/F3 最小修補提案（2026-11-02）

> 使用者核准 B1 限 F1、F3；F5 暫不修改。此為**已產出但尚未在 Ubuntu 套用／測試的補丁提案**，不得標示 B1 完成。

## 來源與版本
- 本機 Git 基線 `e20b294`，tag `round11-baseline`。
- B0 ZIP SHA-256 `c60c2e463b58bfc9100b6845eaa937f2cd12342d81f57135239cac49c50dffa8`。
- ChatGPT 產出 `Shared_Modules_B1_F1_F3_Proposed.patch`（SHA-256 `32a79d3118e4e559511725154cf42e7bdd4929a5f4650badbdabce8ca58b3276`），及完整 Markdown 審查報告（SHA-256 `6b0a8710fcd8778defd5aef14981a50413821a7714d8c7b9646f9ff7bd055e42`）；檔案由對話下載，不含密碼。**補丁尚未提交到本 GitHub 文件庫，也未套用 Ubuntu**。

## 精確修改範圍
1. `backend/app/Actions/Media/OfflineLicenses.php`：F1 `grace` key 缺少 `grace_until` 時不能落入 `ok`，改回 `rekey_required`。
2. `backend/app/Http/Controllers/Api/AuthController.php`：F3 非預期郵件例外保留忘記密碼中性回應，僅記不含個資與驗證碼的 warning；中性 `sent=true` **不代表送達**。
3. `backend/tests/Feature/MediaCenterTest.php`：新增 F1 邊界測試。
4. `backend/tests/Feature/B1PasswordForgotTest.php`：新增 F3 帳號枚舉中性回應、郵件錯誤測試。

## 驗證
- 隔離工作目錄 `php -l` 四檔 PASS。
- 重新解壓的 B0 ZIP 上 `git apply --check` PASS。
- Pest/Concurrency/Flutter/Android **NOT RUN**；Round11 Feature 162 PASS 為前次實測，不能當本補丁驗收。
- 不修改 Schema、Docker Compose、iStudy、非測試 DB；不建立分支或零碎 Commit。
- 待使用者在 Ubuntu 下載並套用補丁，確認專用 `shared_pest_runner`、`shared_pest_test` 和 TestDatabaseGuard 後才進行目標測試與全套回歸。

## 限制
- F5 仍為 P1 待驗證；F2/F4/F6–F9 不擴大。
- 郵件寄送失敗後 OTP 可能已持久化，屬既有流程；本輪未改 OTP schema、Outbox 或排程。
- 發現失敗須保留原始證據，集中修正後才建議一次本機 Commit。
