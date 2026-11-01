# Ubuntu｜Shared Modules V1 MVP 固定環境（2026-11-01）

> 狀態：實機日誌確認；**環境快照，不是需求規格，也不是安全憑證清單**。來源：使用者 2026-11-01 Ubuntu 日誌；後續發現變更時更新本檔，避免新對話重複詢問。

## 開發主機
| 項目 | 實際資訊 |
|---|---|
| OS | Ubuntu 26.04.1 LTS x86_64 |
| Docker / Compose | Docker 29.8.2／Compose v5.6.0 |
| PHP / Composer | PHP 8.4.27（容器）／Composer 2.10.3 |
| MySQL | 8.4.13，容器 `eachcore-dev-mysql-1` |
| Laravel / Pest | Laravel 13.39.0／Pest 4.7.8 |
| Node | v24.22.1 |
| Flutter | `/home/media/GitDev/tools/flutter/bin/flutter` |
| Docker Compose | `/home/media/GitDev/DevEnv/compose.yaml` |
| Docker project | `eachcore-dev` |
| PHP 容器 | `eachcore-dev-php-1` |
| MySQL 主機映射 | `127.0.0.1:33087 -> 3306` |

## 工作區（**不可混為同版**）
- **本輪 ZIP MVP 版本**：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/`
- Laravel 後端：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend`，Docker 容器 `/workspace`。
- Flutter：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/flutter`。
- `/project` 曾用於 Page Atlas 的 repo root；使用前確認實際唯讀掛載。
- **另一份舊 GitHub PoC**：`/home/media/GitDev/ShareModules`。不可覆蓋、混用或視為本輪 MVP 程式基線。
- iStudy Docker、PostgreSQL、ZFS 皆獨立，**不動**。

## 資料庫用途（禁傳密碼）
| DB | 用途與限制 |
|---|---|
| `shared_mvp_test` | ZIP MVP 實機驗收；**不可跑 Pest** |
| `shared_pest_test` | **唯一 Pest 測試專用 DB** |
| `shared_dev`、`shared_test` | 既有舊 DB，不因本交接而刪除或修改 |
| iStudy PostgreSQL | 與 Shared Modules 完全隔離 |

- MySQL 8.4 Migration 驗收已有 **25/25 PASS**（對應本機 MVP），不需無故重跑或重置。
- 容器一般環境變數可能仍設定 `DB_DATABASE=shared_mvp_test`，每次執行測試須顯式覆蓋。
- `phpunit.xml` 的 DB 指定及 `TestDatabaseGuard` 防護不得關閉。
- 不提交 `.env`、密碼、Token、憑證、真實會員資料。

## 執行規範
請先讀 [Pest 資料庫安全](../testing/pest_database_safety.md)，再從 [最新 Debug 交接](../handoff/ubuntu_mvp_debug_20261101.md) 接續。
