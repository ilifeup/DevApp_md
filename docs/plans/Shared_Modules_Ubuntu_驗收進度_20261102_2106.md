# Shared Modules V1｜Ubuntu 實測進度（2026-11-02 21:06 Asia/Taipei）

> 狀態：**實際指定測試 PASS；整體 47 模組／批次 G 尚未正式結案**。本文件只更新可核對的實作與測試證據，不修改模組正式需求。

## 來源基線
- Ubuntu 本機程式工作目錄：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1`；分支 `master`，2026-11-02 21:02 查詢時工作樹乾淨。
- 2026-11-02 11:22 `e20b294` — `baseline: round11 local MVP snapshot`。
- 2026-11-02 13:34 `e84ee1e` — `fix(b1): harden offline license grace and password recovery`。變更 `OfflineLicenses.php`、`AuthController.php`、`B1PasswordForgotTest.php`、`MediaCenterTest.php`。
- 2026-11-02 14:41 `264ff1f` — `fix(concurrency): prevent resource conflicts and booking overselling`。變更 `Capacity.php`、`ResourceConflicts.php`、`BatchGRaceTest.php`。
- **注意：上述 SHA 均來自使用者 Ubuntu 本機 Git，不能推定已推送至任何遠端程式碼儲存庫。**

## 2026-11-02 21:06 指定 Pest 實測
使用既有 `shared-test.sh` 呼叫 Docker PHP 容器 Pest；前置檢查包括容器、MySQL 可用、DNS、觸發器設定、專用 Pest 帳號及 `shared_pest_test` 隔離資料庫連線。

| 指定測試路徑 | Tests | Assertions | 判定 |
|---|---:|---:|---|
| `tests/Feature/B1PasswordForgotTest.php` | 2 | 8 | **PASS** |
| `tests/Feature/MediaCenterTest.php` | 9 | 27 | **PASS** |
| `tests/Concurrency/BatchGRaceTest.php` | 4 | 10 | **PASS** |
| **本輪指定測試合計** | **15** | **45** | **PASS** |

- 另有先前 `tests/Schema` 預設執行紀錄：**10 tests、31 assertions PASS**，與本輪指定測試分開記錄；不將兩次紀錄誤當作完整 Pest suite。
- 測試證據為使用者上傳的 Ubuntu 日誌 `SharedModules_B1_Concurrency_20261102_210619.log`，其中每組均有 `PASS: Pest tests completed`。
- 本輪 15 個指定案例 **PASS**，不代表 B1 所有非測試場景、所有併發時序、全套 API、Flutter、Android 實機或整體批次 G 都通過。

## 專案範圍與未完成驗收
- 正式最新模組數：**47（P0 33／P1 10／P2 4）**；47 是範圍數，不是 47 項已驗收數。
- 批次 G：A～F 最終整合驗收；其併發指定測試 4/4 PASS，**完整 G 尚未有統一驗收摘要**。
- 尚需：完整隔離 Pest suite、必要 MySQL migration/rollback 與並發矩陣、Flutter analyze/test/build、三 App 隔離環境測試及 Android 實機；缺實測結果則記 **NOT RUN／BLOCKED**，不得寫 PASS。
- 第三方需憑證的 FCM、Google OAuth、Woo、正式金流，不能以假服務通過代替真實驗收。
- `backend/storage/framework/testing/disks` 曾在唯讀掃描中出現 Permission denied，是否影響測試尚需獨立查明；不能由此直接判斷失敗。

## 進度與交接要求
1. 先保留已通過的兩次本機修正 commit，不為了重複確認而重新修改程式。
2. 使用已建立的 `shared-test.sh` 和隔離測試 DB；保留命令、時間、測試名稱、退出碼、完整日誌。
3. 驗收結果逐項標記 `PASS / FAIL / NOT RUN / BLOCKED`。CI 綠燈或檔案存在皆不等於驗收。
4. 相關問題集中修正，避免零碎 commit；不自動合併 PR、不碰正式資料庫。
5. 遠端 Ubuntu 管理控制台另案開發，不以它的開發進度覆蓋 Shared Modules 47 模組追蹤。

## 原始本機證據檔名
- `SharedModules_B1_驗收查詢_20261102_210217.txt`
- `SharedModules_驗收結果_20261102_210358.txt`
- `SharedModules_B1_Concurrency_20261102_210619.log`

> 本報告經使用者提供的終端機輸出及日誌核對；非直接連線 Ubuntu 主機重新執行測試。
