# Ubuntu Admin WebApp｜實機隔離測試紀錄（2026-11-03）

## 已驗證環境
- Ubuntu 26.04.1；主機 Python 3.14.4，unittest 可用。
- PHP 容器映像 `eachcore/php:8.4-dev`：PHP 8.4，SQLite 擴充可用，**沒有 python3**。
- Python 容器映像 `istudy/agent:1.0.0-dev.61`：Python 3.12.3，預設 UID 10001，**沒有 PHP**。
- 使用 `docker run --rm` 建立一次性測試容器，沒有操作正在運行的 iStudy/EachCore 容器。

## 實測結果
- PHP Security：42/42 PASS。
- PHP UTF-8 log boundary：2/2 PASS。
- PHP SQLite Recovery diagnostics：PASS（orphan、pagination、transaction）。
- Python 41 個測試模組：初次 40 PASS / 1 FAIL；`test_broker_authorization_flow.py` 在**修正後的容器內測試副本**中 3/3 PASS，因此 41 個模組均有通過紀錄，但**原始專案尚未修正**。
- 以上為單元/局部整合測試，**不代表 API→Worker→SQLite→Web UI、UEM、Cloudflare Tunnel 或手機端已驗收**。

## 下一步
1. 將 Broker 測試 Mock 修正回原始程式庫並在一致的 root-owned 隔離測試副本重跑。
2. API→Worker→SQLite 端到端測試：既有 PHP 與 Python 映像分開運行，使用隔離測試儲存區；先審查腳本副作用。
3. Web UI、UEM、Cloudflare Tunnel、手機 Chrome 驗收。
4. 禁止未審查直接執行部署腳本或會操作主機帳號、systemd、pkill 的 E2E 腳本。
