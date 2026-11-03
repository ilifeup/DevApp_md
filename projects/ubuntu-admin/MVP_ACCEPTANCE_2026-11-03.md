# Ubuntu Admin WebApp｜MVP 實機驗收進度（2026-11-03）

## 目前階段
- MVP 主要程式已打包，使用者已在 Ubuntu 26.04.1 解壓，目錄為 `/home/media/ubuntu-admin-test/ubuntu_admin_webapp`。
- 已確認目錄存在：`broker/`、`db/`、`deploy/`、`docs/`、`tests/`、`uem/`、`web/`、`worker/`。
- 前期本地 PHP API、日誌 UTF-8、Worker／SQLite 等**局部測試**曾通過；不能視為 Ubuntu 端對端驗收。
- **P0 未結案；尚未正式部署，未完成手機 Chrome、UEM、Cloudflare Tunnel 與 API→Worker→SQLite→Web UI 端對端驗收。**
- 目前主機 PHP CLI 不在 PATH；Docker PHP 8.4.27／8.3.26 均有 SQLite 與 mbstring 擴充。詳見 [PHP_DOCKER_2026-11-03.md](PHP_DOCKER_2026-11-03.md)。

## 下一步（MVP 優先，禁止過度開發）
1. 先審查專案現有測試與 Docker PHP 映像相容性；以獨立一次性容器進行無破壞測試。
2. 在隔離環境驗證 PHP API → Worker → SQLite → Web UI，確認 CLI、任務狀態、日誌複製與原始 `.md` 下載。
3. 驗證 UEM 顯示及 Cloudflare Tunnel；需要新增套件或修改主機設定時先告知使用者。
4. 只修復實際驗收阻礙，不新增無關安全模組或功能。

## 安全界線
- 不直接執行 `sudo deploy/install.sh`。
- 不動現有 iStudy／EachCore／Docker 生產服務。
- systemd/cgroup 終止證據不足時不得自動解除 SQLite 資源鎖。
- 不把本地單元測試 PASS 宣稱為實機驗收 PASS。
