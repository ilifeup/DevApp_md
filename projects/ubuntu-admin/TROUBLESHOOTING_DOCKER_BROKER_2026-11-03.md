# Ubuntu Admin WebApp｜Docker / Broker 問題與解法（2026-11-03）

## 1. PHP 映像不能執行 Python
- 現象：`exec: "python3": executable file not found`。
- 原因：`eachcore/php:8.4-dev` 不含 Python。
- 解法：用既有 `istudy/agent:1.0.0-dev.61` 映像執行 Python 3.12.3；不要在主機上重複安裝 PHP/Python。

## 2. 唯讀掛載內建立 tmpfs 掛載點失敗
- 現象：在唯讀 `/app` 下掛載 `/app/tests/python/__pycache__`，OCI runtime 報 read-only file system。
- 解法：移除該 tmpfs 掛載，設定 `PYTHONDONTWRITEBYTECODE=1`，僅提供 `/tmp` tmpfs。

## 3. Broker 授權測試回傳 126
- 現象：`核准紀錄目錄權限不安全`。
- 原因：Python Agent 映像預設 UID 10001，測試 `TemporaryDirectory` 雖為 0700，但擁有者不是 UID 0；Broker 要求 root-owned。
- 解法：**只在一次性隔離容器**指定 `--user 0:0`，保留 `--cap-drop ALL`、`--security-opt no-new-privileges`、`--network none`、`--read-only`。不可放寬正式 Broker 權限。

## 4. 改為 root 後回傳 127
- 現象：`test_broker_authorization_flow.py` 仍失敗，回傳 127。
- 原因：測試把 `broker.open_verified_directory` 全域 Mock 成核准紀錄目錄；Broker 在核准後又用該函式讀取 `module_integrity.py` 與輔助模組，錯誤導向測試目錄。原始碼 broker 目錄 UID 1000、775，亦不符合正式 root-owned 安全載入要求。
- 驗證解法：在一次性容器內用 root 複製 `broker`、`tests` 到 `/tmp/ua-test`，移除 broker 副本 group/other write，**只修改副本的 Mock**：當路徑為 `str(BROKER.parent)` 時，開啟該 broker 目錄；其他路徑仍按測試核准目錄處理。結果 3/3 PASS（0.3 秒）。
- 注意：這是暫時驗證，**原始測試程式尚未更新**；正式修正應保留真實安全驗證語意，避免 Mock 掩蓋權限漏洞。

## 5. 主機 /tmp 掛載 Permission denied
- 現象：主機 `mktemp -d` 建立的目錄（通常 0700）掛到容器後，即使容器指定 UID 0，讀取仍可能被拒絕。
- 解法：避免修改主機 /tmp 權限，改以已驗證可讀的原始專案唯讀 bind mount，並在**容器內 tmpfs** 建立獨立副本。

## 6. 其他測試注意事項
- 真實 PHP Recovery 測試檔名：`tests/php/recovery_diagnostics_test.php`，**不是** `recovery_integration_test.php`。
- Python 測試入口：`tests/run_python_isolated.py`；41 個 `tests/python/test_*.py`，每模組預設 90 秒。
- 不直接在主機執行未審查的 `tests/e2e/run_e2e.sh`，可能涉及帳號與程序操作。
- `istudy/agent` 映像無 PHP；跨語言整合測試應考慮兩個獨立容器，共用隔離測試資料，不應直接安裝或動正式服務。
