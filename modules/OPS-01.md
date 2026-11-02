# OPS-01｜管理員環境檢測與開發診斷（P0；長期需求）

## 核准範圍與定位
2026-11-02 使用者要求新增長期 P0 需求，目標是減少 Shared Modules 與三個 App 在本地 Ubuntu／Laravel Cloud 反覆環境排錯的時間。此文件是**需求與設計方向**，不是程式已實作、已測試或已部署的證據。新增 OPS-01 後規劃模組總數 48（原 47 + 1），P0 34／P1 10／P2 4；待後續版本計畫書正式整併。

## MVP 必備需求
- OPS-01-01：Filament Super 管理員專用「系統診斷」頁面／Tab；與三 App 共用程式碼、各 App 獨立 DB／後台，不建立中央控制台。
- OPS-01-02：唯讀檢查 PHP／Laravel 版本、必要 extension、DB 連線與實際 DB 名稱（僅顯示允許的識別資訊）、Migration 狀態、cache／queue／storage 基本可用性、環境種類、關鍵設定是否存在；不輸出密碼、token、.env 內容或完整連線字串。
- OPS-01-03：檢測狀態 PASS／WARN／FAIL／NOT_APPLICABLE／NOT_CHECKED，顯示最後檢查時間、檢查來源、修復建議及可複製的遮罩化摘要；避免「未檢查」誤報「通過」。
- OPS-01-04：Laravel Cloud／一般 Web Hosting／Ubuntu 共用 PHP 偵測器介面，依環境能力啟用檢查；不可假設 Cloud 有 Docker、systemd、SSH 或 Python。
- OPS-01-05：Ubuntu 開發專屬的 Docker／MySQL readiness／DNS／測試 DB Guard 檢查，**優先沿用既有 shared-test.sh 與其 preflight 邏輯**，不得從公開 Web 請求直接執行 shell、Pest、Migration、資料庫重置、sudo 或 Docker 控制。必要時後續另行設計本機受限 Agent，但非 MVP。
- OPS-01-06：所有測試／診斷僅能在明確允許的環境與授權範圍內執行；正式環境不提供破壞性操作。可顯示最近一次由可信來源匯入的測試報告，但不得假裝後台執行過該測試。
- OPS-01-07：對診斷頁加上伺服器端 Super 權限、CSRF、限流、審計及結果遮罩；診斷資訊不得暴露於訪客、一般管理員或公開 API。
- OPS-01-08：優先使用 Laravel/PHP 既有能力，不強制安裝 Python、新套件、額外 DB 表或常駐服務；若需新增依賴或操作能力須先獨立審核。

## 非 MVP／明確禁止
- 任意指令執行、遠端 Shell、瀏覽器一鍵重啟主機／容器、任意編輯 .env、建立 root 權限、在正式資料庫執行 migrate:fresh、掃描其他 App 私有資料、跨 App 共用 DB。
- 自動修復、自動部署、全面監控平台、Python 常駐代理、集中收集機密；均非本次核准範圍。

## 分階段
1. P0-A：純 PHP 唯讀診斷、環境識別、權限與遮罩化複製報告。
2. P0-B：整合現有測試證據與 Ubuntu preflight 結果（本地可信來源，預設不可由 Web 觸發 shell）。
3. 後續提案：需要時才評估受限本機 Agent、明確授權操作；不得自動納入。

## 驗收條件
- Super 可看，其他角色／訪客 403；跨 App 不洩漏資料。
- Laravel Cloud 不支援的 Ubuntu 項目顯示 NOT_APPLICABLE，不顯示 FAIL。
- 不輸出 secrets；失敗不造成 HTTP 500、資料庫修改或破壞性動作。
- 測試報告明確區分「即時檢測」與「歷史匯入」；具時間戳、來源與證據。
- 未取得 Ubuntu／Cloud 實測證據之前標示 NOT VERIFIED。
