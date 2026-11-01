# Ubuntu Shared Modules MVP｜2026-11-02 實機環境與驗收快照

> 目的：新 AI 不必反覆詢問既知環境。此文件是**實機測試快照**，不含密碼、Token、憑證，也不是正式部署說明。先讀 [原固定環境](ubuntu_shared_modules_mvp.md)、[測試安全](../testing/pest_database_safety.md)、[Debug 防重犯索引](../testing/DEBUG_PLAYBOOK_20261102.md)。時間以台灣 UTC+08:00 記錄。

## 1. 主機與已確認軟體
| 項目 | 值 | 證據性質 |
|---|---|---|
| 主機 OS | Ubuntu 26.04.1 LTS x86_64 | 先前實機檢查；本輪未重新讀取 OS 版本 |
| Docker | 29.8.2 | 先前實機檢查 |
| Docker Compose | v5.6.0 | 先前實機檢查 |
| Docker project | `eachcore-dev` | 既有環境 |
| PHP image | `eachcore/php:8.4-dev` | 2026-11-02 02:37 Docker ps |
| PHP CLI | 8.4.27 | 2026-11-02 02:37 Laravel about |
| Laravel | 13.39.0 | 同上 |
| Composer | 2.10.3 | 同上 |
| Pest | 4.7.8 | 先前實機檢查 |
| MySQL | 8.4.13 | 先前實機檢查 |
| Node.js | v24.22.1 | 先前實機檢查 |
| Flutter SDK | `/home/media/GitDev/tools/flutter/bin/flutter` | 先前實機路徑；本輪未驗證 Flutter build |
| Laravel environment | `testing` | 2026-11-02 02:37 診斷 |
| Laravel Debug Mode | OFF | 同上 |
| Laravel timezone | UTC | 同上，**不是台北時區**；時間處理需另查正式契約 |
| Laravel locale | en | 同上 |
| Laravel config cache | MISSING | 2026-11-02 02:37 診斷 |

以上不是「已安裝所有開發套件」的證明；不可推斷 Flutter Android SDK、JDK、FCM 實際服務可用。

## 2. 主機路徑與容器掛載
| 用途 | 路徑／名稱 |
|---|---|
| 使用者 HOME | `/home/media` |
| 本輪 ZIP MVP 根目錄 | `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1` |
| 後端主機路徑 | `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend` |
| Laravel Docker workdir | `/workspace`（對應後端） |
| MVP Docker 專案根 | `/project`（Page Atlas 路徑用途；實際 mount 以 inspect 為準） |
| Flutter 目錄 | `/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/flutter`（先前環境記錄；本輪 ZIP 原始碼快照**沒有 Flutter**） |
| Docker Compose | `/home/media/GitDev/DevEnv/compose.yaml` |
| PHP container | `eachcore-dev-php-1` |
| MySQL container | `eachcore-dev-mysql-1` |
| MySQL host mapping | `127.0.0.1:33087 → 3306` |
| 舊 Git PoC（不可混用） | `/home/media/GitDev/ShareModules` |
| 既有 iStudy 容器／PostgreSQL | 完全獨立；不操作 |

`Shared_Modules_V1` 是**ZIP 解壓工作樹，不是 Git repository**；可使用 `git apply` 檢查／套用 patch，但不能要求 `git status` 必須成功。此文檔所在的 `ilifeup/DevApp_md` **是文件 repo，不是 Ubuntu 已測程式碼 repo**；不能聲稱已將 Round1–11 原始碼同步至 GitHub。

## 3. 資料庫用途（重要）
| 名稱 | 用途 |
|---|---|
| `shared_pest_test` | **唯一允許 Pest 測試的 DB**；測試可能重置資料 |
| `shared_mvp_test` | ZIP MVP 驗收資料庫，**不得用於 Pest** |
| `shared_dev`、`shared_test` | 舊 DB，不自動清理 |
| iStudy PostgreSQL | 禁止操作 |

`backend/.env.testing` 已確認四項：
```dotenv
APP_ENV=testing
DB_CONNECTION=mysql
DB_DATABASE=shared_pest_test
DB_TEST_EXPECTED_DATABASE=shared_pest_test
```
**注意：**Docker 容器預設可能是 `shared_mvp_test`。即使 `APP_ENV=testing`，Laravel `TestDatabaseGuard` 仍會在啟動時拒絕 DB 不符，實測錯誤：
`DB_DATABASE must be 'shared_pest_test' (got 'shared_mvp_test')`。這是正確防護，**不要關閉**。

## 4. 已確認 Push 設定（非機密）
2026-11-02 02:37，於容器內以 testing + Pest DB 明確啟動 Laravel：
```text
APP_ENV = testing
database.default = 'mysql'
database.connections.mysql.database = 'shared_pest_test'
push.max_tokens_per_user = 10
push.claim_ttl_seconds = NULL
push.max_attempts = NULL
config/push.php = EXISTS
bootstrap/cache/config.php = MISSING
```
- `NULL` 只表示該 config key 未設定，**不能推論 Job 沒有內建常數或預設**。
- 先前「裝置數量上限為 0」假設已排除。Push 三個問題的確認根因為 `renew()` MySQL no-op update 受影響列數誤判，Round11 已修並通過測試。
- 不把 FCM 服務帳戶、金鑰、裝置 token 或完整 `config:show push` 輸出上傳。

## 5. 可重現的 Feature 測試命令
在 Ubuntu 已開啟的終端機中執行（**不必安裝主機 PHP**）：
```bash
cd /home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1 || exit 1
for item in APP_ENV=testing DB_CONNECTION=mysql DB_DATABASE=shared_pest_test DB_TEST_EXPECTED_DATABASE=shared_pest_test; do
  grep -Fxq "$item" backend/.env.testing || { echo "Unsafe test config: $item"; exit 1; }
done

STAMP="$(date +%Y%m%d-%H%M%S)"
LOG="$HOME/mvp-debug-logs/feature-$STAMP.log"
mkdir -p "$HOME/mvp-debug-logs"

docker exec -w /workspace \
  -e APP_ENV=testing \
  -e DB_CONNECTION=mysql \
  -e DB_DATABASE=shared_pest_test \
  -e DB_TEST_EXPECTED_DATABASE=shared_pest_test \
  eachcore-dev-php-1 \
  php -d memory_limit=512M \
  ./vendor/bin/pest tests/Feature --colors=never \
  > "$LOG" 2>&1
RESULT=$?
grep -E 'Tests:|Duration:|FAILED|Fatal error' "$LOG" | tail -30 || true
echo "Pest exit code: $RESULT"
echo "Log: $LOG"
```
**PHP memory_limit：**原本 CLI 128 MiB，18 MiB 加密媒體測試在 `MediaKeyring.php:122 openssl_encrypt` OOM；單次測試使用 512 MiB 即完成全套。**沒有修改正式 php.ini**。

## 6. 備份、修補與日誌位置
- 下載 ZIP 預設：`/home/media/Downloads/`。
- 原始碼備份：`/home/media/mvp-debug-backups/`；已知成功備份例 `mvp-source-20261102-021507.tar.gz`（約 2.3 MB，排除 storage/vendor/env），其餘備份檔名帶 timestamp。
- 日誌目錄：`/home/media/mvp-debug-logs/`。
- Round4–8 合併 ZIP：`shared_mvp_debug_round4to8_merged_20261102.zip`。
- Round9：`shared_mvp_debug_round9_20261102.zip`。
- Round10：`shared_mvp_push_diagnostic_round10_20261102.zip`。
- Round11：`shared_mvp_push_round11_20261102.zip`。
- 第十一批最終完整回歸證據：`mvp-feature-final-round11-20261102-024503.zip`。
- 每次 patch 先 `git apply --check`，已套用用 `git apply --reverse --check`；**不重複套用**。Patch 是增量且有基線順序，不可隨意跨版本。
- 不覆蓋 `.env`、`.env.testing`，不備份／分享 secrets。

## 7. 2026-11-02 最終回歸矩陣
| 範圍 | 最近證據 | 狀態 |
|---|---|---|
| Laravel Feature 全套 | 162 PASS／0 FAIL、691 assertions、46.70 秒（Round11） | **PASS** |
| Push 三個針對性測試 | 3 PASS、21 assertions、12.23 秒 | **PASS** |
| Round9 ACCESS/SHOP targeted | 38 PASS／3 FAIL；失敗均屬當時未修的 Push | **後續已修** |
| Migration | 25/25 PASS（先前測試） | **歷史 PASS** |
| Unit | 43 PASS／99 assertions（先前測試） | **歷史 PASS，Round11 後未重跑** |
| Schema | 91 PASS／120 assertions（先前測試） | **歷史 PASS，Round11 後未重跑** |
| Concurrency | 無本輪結果 | **NOT RUN** |
| Flutter、Android/iOS build、E2E | 無本輪結果 | **NOT RUN** |
| 真實 FCM、Woo 外部整合 | 使用 fake/mock；未證明真實服務 | **NOT RUN** |
| 正式部署與資料安全驗收 | 無本輪結果 | **NOT RUN** |

**安全結論：**本次僅後端 Feature 162 項驗收通過。不能宣稱全部 MVP 或正式部署已完成。所有測試以實際最新輸出為準，歷史狀態不得覆蓋最新結果。
