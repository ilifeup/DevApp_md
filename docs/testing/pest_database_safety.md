# Shared Modules V1｜Pest 專用資料庫安全

> 本文件只記錄本機測試操作安全；不能改寫 `modules/` 的正式業務規格。資料庫防護必須先於破壞性測試。

## 絕對限制
- **只允許在 `shared_pest_test` 跑 Pest**，不可跑 `shared_mvp_test`、`shared_dev`、`shared_test` 或任何 iStudy DB。
- `TestDatabaseGuard` 必須檢查 `APP_ENV=testing`、MySQL 連線、實際 DB 與 `DB_TEST_EXPECTED_DATABASE` 一致。不得略過 Guard、刪測試、停用外鍵或為綠燈放寬權限。
- `tests/Concurrency` 包含 `migrate:fresh` 和建立／刪除測試表，**先實機確認 Guard 成功攔截錯誤 DB**，才執行。
- 操作前確認容器和 mount；任何環境不一致應停止破壞性測試。

## Pest 指令模板
```bash
cd /home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend
docker exec -w /workspace \
  -e APP_ENV=testing \
  -e DB_CONNECTION=mysql \
  -e DB_DATABASE=shared_pest_test \
  -e DB_TEST_EXPECTED_DATABASE=shared_pest_test \
  eachcore-dev-php-1 \
  ./vendor/bin/pest tests/Feature --colors=never --compact
```
將 `tests/Feature` 換成 `tests/Unit`、`tests/Schema`、特定檔案；**Concurrency 需先確認防護**。

## 安全執行順序
1. 只讀：`docker inspect`、`git status`、檢查 DB 設定和掛載。
2. PHP lint 與不寫 DB 的靜態檢查。
3. Pest Unit、Schema、Feature。
4. 安全 Guard 實際驗證後，再跑 Concurrency。
5. 未測標 `NOT RUN`，不可把舊結果冒稱當前 PASS。

## 2026-11-02 已驗證執行基準（不要重複環境盤查）

- 以上四個 `-e` 變數配合 `php -d memory_limit=512M` 已成功跑 B1 targeted **11 PASS／35 assertions／15.35s**、full Feature **165 PASS／701 assertions／49.12s**。未發生 Guard 拒絕或連線錯誤。
- 已在 Ubuntu 確認 `shared_pest_runner`@'%' 存在、`shared_pest_test` 存在、帳號權限限於該測試 DB。**實際 Pest 連線使用者尚未另行證明**，不可推定。不得為重查而更換密碼、重建帳號或修改 Docker。
- 既有容器 Laravel root 是 `/workspace`（即宿主機 `backend/`），檔案路徑不可多加 `backend/`；終端命令不得直接使用 `exit`／子 Shell 外的 `return`。
- 下一輪直接重用已驗證命令，僅在有實際異常或環境變更時檢查相關項目。
