# Ubuntu Admin WebApp｜Claude 沙盒相容性與 SQLite＋WAL 決策
> 2026-11-02｜來源：使用者提供的 Claude 環境檢查與跨程序實測回覆。**證據性質：Claude 自述測試；本庫未取得原始測試日誌或獨立執行。**
## 1. 範圍、狀態及裁定
- 系統：**Ubuntu Admin WebApp**，非 Shared Modules V1 主要應用資料庫。
- **核准方向**：改用 **SQLite + WAL 作為此系統唯一正式資料庫**；停止依賴 MySQL/PyMySQL，完整移植後必須重跑沙盒 E2E 和正式 Ubuntu 驗收。
- **目前狀態**：`DECISION_APPROVED / IMPLEMENTATION_NOT_STARTED / HOST_UNVERIFIED`（使用者本次訊息已要求 DB 選擇正確後再開始，Claude 回報本次未改正式程式）。
- 不改 Shared Modules、iStudy、三 App 既有 DB 契約；不觸及正式主機資料。
## 2. 沙盒環境（Claude 回報）
| 組件 | 結果 |
|---|---|
| PHP `pdo_sqlite`, `sqlite3` | 可用；SQLite library 3.45.1 |
| Python 3.13 `sqlite3` | 可用；SQLite library 3.53.1 |
| PostgreSQL Server | 16.15；臨時 /tmp instance 啟動成功，後已停止 |
| PHP `pdo_pgsql` | 可用 |
| Python `psycopg/psycopg2` | 不存在；pip 一次嘗試遭網路政策擋下 |
| Python `pymysql` | 不存在；舊 Worker 在沙盒未完成 import 驗證 |
| MySQL Server | 套件來源受沙盒網路政策限制，無可用驗證服務 |
注意：PHP/Python 可能連結不同 SQLite library 版本；共享 DB 的檔案格式、SQL feature、Extension 與 PRAGMA 皆須用雙端的共同支援特性驗證。
## 3. SQLite 真實跨程序實測摘要（均為 Claude 回報）
| 項目 | 回報結果 |
|---|---|
| WAL | `journal_mode=wal` 生效 |
| 20 PHP processes，同 task/args、不同 idempotency keys | 建立 1 筆，其餘 19 筆回傳既有 Run |
| 15 PHP processes，同 key、不同 args | 建立 1 筆，其餘 14 筆回傳既有 Run |
| Python 讀 PHP 資料 | 通過 |
| 4 Python Worker + 10 PHP submitters、70 Runs | 每筆只領一次；running 同時最多 2；resource locks 釋放，integrity_check 通過 |
| UNIQUE + ROLLBACK | 全交易回滾，無部分寫入 |
| WAL 讀/寫併行 | Python 持有寫入交易時，PHP 讀取於 0.03s 內完成 |
| 第二寫入者的 busy_timeout | 約 1.5s 後回報 locked（預期） |
| deferred transaction 讀升寫 | 實測有失敗；寫入流程要求 `BEGIN IMMEDIATE` |
| 不同 Unix account 共用 SQLite | 需預先正確配置 DB 檔、目錄及 sidecar permissions |
這些是 **PoC PASS (CLAUDE-REPORTED)**，不是既有完整 WebApp Migration、API、Worker 的 PASS。
## 4. 權限要求
- 專用 DB 目錄 `/var/lib/ubuntu-admin/db/`，`2770`（setgid），僅允許 `ua-web` 和 `ua-worker` 所屬的專用共用群組。
- DB 檔預先建立、權限 `0660`；不能只倚賴 umask。須實測 `-wal`、`-shm` 建立/重建、群組繼承與 ACL/實際可寫入性。
- 本次 Claude 回報其 PoC 中 DB 檔新建權限為 0640，且在改成 0660/2770 後雙帳號通過；**不可把特定 PoC 的 0640 視為 SQLite 所有環境固定預設**。
- DB 檔不在 Web public root；維持 least privilege、root broker HMAC 秘鑰與 Worker 分離；SQLite 沒有 DB role/DDL 隔離，必須列為風險。
## 5. MySQL → SQLite 整批修改清單
1. Migration：ENUM→CHECK、JSON→TEXT+json_valid、AUTO_INCREMENT→INTEGER PRIMARY KEY、時間 UTC ISO、外鍵及 `PRAGMA foreign_keys=ON`。
2. `web/src/Db.php`：PDO SQLite、foreign_keys/busy_timeout、WAL（建置/初始化時確立，避免每次連線重複模式切換）、synchronous、`BEGIN IMMEDIATE` 寫入交易；不可只看 SQLite 錯誤碼 19，還應辨別 UNIQUE/CONSTRAINT 等 extended code 或特定 constraint。
3. SQL：約 15 處 MySQL 語法：`FOR UPDATE`、`ON DUPLICATE KEY`、`INSERT IGNORE`、`UTC_TIMESTAMP()`、`INTERVAL`、多表 DELETE。
4. Python Worker：`pymysql`→標準庫 `sqlite3`；原子領取、最多 2 running、資源鎖、lease/recovery/cancel 用短交易實作，避免長任務持有 DB writer lock。
5. Deployment：移除 MySQL 建帳號及依賴；`install.sh` 初始化 2770 目錄、0660 檔與專用 group，`migrate.php` 改 SQLite，加入 Python backup API 線上備份、復原演練及 WAL/SHM 配套。
6. Testing：替換 MySQL 專用測試；新增真實 PHP API E2E、跨語言 concurrency/idempotency、Worker cancel/restart/recover、schema migrations、錯誤權限測試。避免僅用 mocks 或 lint 宣稱完成。
7. Deployment validation：Ubuntu 26.04.1、實際 PHP/Python 版本、兩帳號權限、systemd、Broker、Cloudflare、UEM 實機驗收；未跑項目保留 UNVERIFIED。
## 6. 選型比較
| 候選 | 沙盒實測 | 優勢 | 阻礙/風險 | 本系統結論 |
|---|---|---|---|---|
| SQLite + WAL | PHP/Python PoC 通過 | 零 DB Server、原生 sqlite3、單管理員/最多兩個 Worker 適用 | 單一 writer、共享檔權限、沒有獨立 DB roles | **採用** |
| PostgreSQL 16 (sandbox) / 18 (host) | PHP driver 可用，Python driver 缺 | 交易/佇列/角色能力完整 | 沙盒 Python E2E 無法跑 | 暫不採用 |
| JSON files | 可讀寫，但沒有相當 DB PoC | 無 server | 需自行實作交易、唯一約束、並行與崩潰復原 | 不作主要 DB；只供設定、匯出、報告 |
## 7. 執行與驗收關卡
- 本決議不是對已有 Release 的功能驗收。原 ZIP/MySQL 版本的 Migration、Worker、API E2E 仍屬 `UNVERIFIED`。
- 下一步由 Claude **一次集中移植** Web/Python Worker/Migration/部署/測試，不要並存三套 DB；同時補齊所有已核准功能後才輸出完整 ZIP。
- 每項結果紀錄 requirement ID、source path、test command、real output、timestamp、環境與 PASS/FAIL/UNVERIFIED；最終 ZIP 須誠實分開「沙盒驗收」與「Ubuntu 正式機驗收」。
- 所有破壞性 DB 測試必須由程式強制限制在一次性 disposable DB，不是只在文字寫「請勿用正式資料庫」。
