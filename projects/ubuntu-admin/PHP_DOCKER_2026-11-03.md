# PHP／Docker 已驗證資訊與安裝禁令（2026-11-03）

## 實際檢測
| 容器 | PHP CLI 版本 | mbstring | pdo_sqlite | sqlite3 |
|---|---|---|---|---|
| eachcore-dev-php-1 | 8.4.27（2026-10-28 build） | 有 | 有 | 有 |
| istudy-php-1 | 8.3.26（2025-10-21 build） | 有 | 有 | 有 |

檢測指令：
```bash
docker exec eachcore-dev-php-1 php -v
docker exec eachcore-dev-php-1 php -m | grep -Ei 'pdo_sqlite|sqlite3|mbstring'
docker exec istudy-php-1 php -v
docker exec istudy-php-1 php -m | grep -Ei 'pdo_sqlite|sqlite3|mbstring'
```

## 重要更正
先前因 Ubuntu 主機 `php: command not found` 而直接建議 `sudo apt install php8.5-cli php8.5-sqlite3 php8.5-mbstring php8.5-xml`，**這個建議過早，應撤回**。PHP 已在 Docker 容器內運作，並具備測試所需三項擴充。**沒有證據需要安裝主機 PHP 8.5。**

## 後續 AI 強制檢查順序
1. 查詢 `docker ps`、映像與 `docker exec <容器> php -v/-m`。
2. 確認測試需求、PHP 版本相容性、檔案掛載與容器權限。
3. 優先考慮 `docker run --rm` 使用既有映像建立隔離容器（需先確認映像啟動設定）；不可直接在正式服務容器執行具破壞性的測試。
4. 僅在確有必要且經使用者同意後，才建議新增套件、服務或主機層變更。
5. 不要因主機 `php` 不存在就重複建議安裝 PHP 8.5。
