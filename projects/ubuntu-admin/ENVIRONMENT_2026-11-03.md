# Ubuntu 主機環境快照（2026-11-03）

> 來源：使用者於 Ubuntu 終端機提供的實際命令輸出。此文件為當日快照，後續操作前仍須重新檢查。**禁止以主機找不到 php 指令推論 PHP 未安裝。**

## 主機
- Ubuntu 26.04.1 LTS；Python 3.14.4。
- 專案解壓目錄：`/home/media/ubuntu-admin-test/ubuntu_admin_webapp`。
- `command -v php` 無輸出，主機 PATH 中無 PHP CLI。
- `sqlite3`：`/home/media/GitDev/tools/android-sdk/platform-tools/sqlite3`（Android SDK 路徑，不代表系統 SQLite 套件）。
- `curl`：`/usr/bin/curl`；`systemd-run`：`/usr/bin/systemd-run`。
- `cloudflared` 主機命令未找到；尚未安裝。
- `systemctl is-active nginx apache2 php8.3-fpm php8.4-fpm` 均回報 inactive；**不能據此判斷 Docker 內 nginx/PHP 停止**。

## Docker 正在執行的容器
| 容器 | 映像檔 | 當時狀態 |
|---|---|---|
| eachcore-dev-php-1 | eachcore/php:8.4-dev | Up 24 hours |
| eachcore-dev-mysql-1 | mysql:8.4 | Up 24 hours (healthy) |
| istudy-agent-1 | istudy/agent:1.0.0-dev.61 | Up 24 hours |
| istudy-php-1 | istudy/php:1.0.0-dev.61 | Up 24 hours |
| istudy-nginx-1 | nginx:1.28.0-alpine | Up 24 hours |

主機 `ps aux` 可看到 php-fpm master 與 www-data worker，可能屬容器程序；不能據此認定主機已安裝 php-fpm 套件。

## 安全操作原則
1. 先檢查主機、Docker 映像、容器及擴充套件，**不要先執行 apt install**。
2. iStudy、EachCore 與現有 Docker 服務不可因 Ubuntu Admin WebApp 測試而停止、覆蓋或修改。
3. 優先用獨立、一次性測試容器，唯讀掛載專案；避免在正在提供服務的容器內跑測試。
4. 不直接執行 `sudo deploy/install.sh`；需先審查安裝腳本及隔離方式。
5. 本檔是環境紀錄，不代表已完成 Ubuntu Admin WebApp 實機驗收。
