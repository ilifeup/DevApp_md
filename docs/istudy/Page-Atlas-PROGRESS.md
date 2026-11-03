# iStudy Page Atlas｜開發進度與驗收追蹤

- 更新：2026-11-03
- 需求狀態：**使用者已核准 P0／P1 範圍與先後順序**
- 程式狀態：**ChatGPT 工作區回報 P0 已開發並補強；尚未同步 Ubuntu Git main**
- 驗收狀態：**Ubuntu 實機 NOT RUN；不得宣告正式完成**
- 參考計畫：[Page-Atlas-PLAN.md](Page-Atlas-PLAN.md)

## 1. 來源與進度里程碑

| 階段 | 已報告成果 | 證據界線 |
|---|---|---|
| dev.62 原始碼比對 | Git main 與 dev62 主要應用碼未見差異；Preview 僅存在 dev62 工作區 | 依使用者提供 Ubuntu `diff -qr` 與檔案清單 |
| UI Preview | `preview.mjs`、`bridge.js`、`mock.mjs`、`UI-PREVIEW.md`；曾有 Client／Server 編譯與 CSS watch 紀錄 | `UI-PREVIEW.md` 記載原測試環境 socket／Chrome 權限阻擋；畫面未驗證 |
| Preview 整合基線 | 使用者匯出 217 檔、8.65 MB ZIP；Git main 未修改 | 匯出程序成功；不代表執行驗收 |
| P0 第一版 | ChatGPT 工作區回報新增 34 頁 Registry（Client 15／Server 19）、搜尋、來源索引、頁碼角標、AI 摘要 | 第一版 ZIP 曾交付；尚未在 Ubuntu 基線重驗 |
| P0 v1.1 | 回報新增 Preview 隔離測試、HTTP 安全閘門、路徑驗證 | Node 測試回報通過；未進行實際 HTTP／瀏覽器驗收 |
| 後續補強 | 回報搜尋元件/API、導航上一頁下一頁、API/DB 狀態及摘要擴充 | 最近一次對話回報 Node 測試 14/14；本文件尚無獨立 CI log／Commit SHA 可查 |

**14/14 是聊天開發過程中最後一次回報的 Node 測試數，不可當作正式 Ubuntu 驗收結果。** 早期 7/7、11/11、12/12、13/13 為不同階段回報，不應加總。

## 2. 功能追蹤

| 功能 | 程式實作回報 | Ubuntu 驗收 |
|---|---|---|
| 永久頁碼 Registry／唯一性 | 已回報實作（34 頁） | NOT RUN |
| Client／Server 路由覆蓋 | 靜態測試回報通過 | NOT RUN |
| 頁面搜尋／元件與 API 關鍵字 | 已回報實作 | NOT RUN |
| 上一頁／下一頁與搜尋結果定位 | 已回報實作 | NOT RUN |
| React 元件／來源路徑索引 | 已回報實作 | NOT RUN |
| PHP API 路由契約 | 僅來源線索 | **待逐項核對** |
| DB／欄位關聯 | 標示待核對 | **待逐項核對** |
| Super 權限頁碼角標 | 已回報實作 | NOT RUN |
| 複製 頁面摘要給 AI 檢查 | 已回報實作 | NOT RUN |
| Preview Mock 隔離／HTTP 閘門 | 靜態與 Node 測試回報通過 | **HTTP／瀏覽器 NOT RUN** |
| TypeScript 完整檢查 | 曾受缺少 `@types/node` 阻礙 | NOT PASS |
| Client／Server 實際畫面、剪貼簿、SSE | 未完成實機驗證 | NOT RUN |
| P1 Mock／Page Info Text／PNG | 尚未開始 | NOT RUN |

## 3. 待處理工作（依優先順序）

1. **API／DB 契約**：逐項對照 `server/src/router.php`、`server/sql/schema.sql` 與前端呼叫；只將有證據的關聯標為 verified。
2. **權限／定位**：核對 Super 權限是否從可信身分來源取得；頁碼角標不可遮擋 UI；登出清理狀態。
3. **Preview 隔離**：在 Ubuntu 獨立驗收目錄測試 HTTP 非 GET、未知 API、外部連線、Host/CSP、SSE、Mock fixture；確認無正式 API／DB／Agent 出口。
4. **建置**：核對 `package-lock.json`、TypeScript 型別、Client／Server esbuild 編譯及執行時錯誤。
5. **實際 UI**：瀏覽器搜尋、上一頁／下一頁、頁碼顯示、AI 摘要複製。
6. **P0 Gate**：有可重現測試與版本證據後，才能標示通過並安排 P1。

## 4. 管理規則

- 此追蹤檔屬 iStudy，不覆蓋 Shared Modules 的 `tracking/PA-01.md`。
- 規格核准、程式實作、測試驗收為三個不同狀態。
- 無 Git Commit SHA、CI log 或 Ubuntu 測試輸出時，只記「工作區回報」，不得宣稱已正式驗收。
- 使用者要求持續集中開發，**不必每次對話提供 ZIP**；有交付或驗收需要時才打包。
- 不修改正式 Git main、不自動合併 PR、不操作正式資料庫、Agent 或正式教材。
