# Shared Modules V1｜Claude 下一批次整合開發指令

> 基線：`Shared_Modules_V1_Batch4_Final_Handoff_20261031.zip`。參考：https://github.com/ilifeup/DevApp_md 的 `AGENTS.md`、`PROJECT.md`、45 份 `modules/`、r7.2 HTML、`changes/CHG-20261031-MEDIA-01.md`。**45 個模組維持不變。**

## 最高規則

**先清除已確認錯誤 → 檢查 A01–A15 → 核對下一正式批次 → 一次完成整批開發與測試 → 單一 ZIP 交付。** 不得亂開發、過度設計、擅自改規格、增加功能或拆成零碎批次。

## 1. 優先清除 MEDIA-01 錯誤設計

移除**伺服器解密音樂線上串流**及其播放 URL、`/stream` 路由、Flutter `_playOnline`／`mediaStreamUrl`、線上播放按鈕、串流專用 4-slot Cache Lock、503 重試、HLS／Range、無用設定／測試／Page Atlas 引用。刪除前核對呼叫鏈，刪除後全專案搜尋殘留。

**必須保留** `media.cipher` 加密檔下載 URL、AES-256-GCM／X25519／Ed25519、裝置綁定、離線授權最長 365 天、手機加密 Cache、原子切換、本機播放與 seek、影片連結、會員權限。音樂唯一流程：**驗證權限 → 下載完整加密檔 → 驗證寫入快取 → 才能播放**。管理員單曲上傳硬上限 **18 MiB = 18,874,368 bytes**，前後端均驗證，超限不得建立正式檔案或資料。

## 2. 整批核對 A01–A15

| 編號 | 檢查／修正 |
|---|---|
| A01 | BOOK 候補允許重疊；轉正時重驗容量、資格、欠費及衝突 |
| A02 | 授權核發與金鑰輪替共用交易鎖序 |
| A03 | 同裝置不得靜默替換 X25519 公鑰 |
| A04–A05 | 移除線上播放 URL 與串流專用 Cache Lock |
| A06 | 18 MiB 上限及記憶體使用驗證 |
| A07–A08 | 下載／清理競爭；失敗保留舊檔與授權 |
| A09–A10 | 輪替／上傳鎖序；授權查詢不得誤撤銷 |
| A11–A12 | PAYGATE Webhook 去重與交易內重驗 |
| A13 | PA-01 同步併發沿用既有 DB 鎖 |
| A14 | 刪伺服器串流 Range，保留本機 seek |
| A15 | 容量 null 顯示「尚未設定容量」 |

逐項標示 `CONFIRMED`／`ALREADY_CORRECT`／`NOT_APPLICABLE`／`BLOCKED` 並附程式證據。**不存在的問題不修；不得為了清單新增架構。**

## 3. 下一整批開發

核對 r7.2 的批次計畫與現有 ZIP／`docs/PROGRESS.md`，確定**下一批尚未完成且已核准**的模組、需求 ID、相依及驗收條件。確認後一次完成該批的 Laravel、Filament、Flutter、必要 Migration、測試與文件。若範圍不明，只詢問必要決策，**不得自行選模組或發明功能**。

追蹤完整呼叫鏈：Flutter／Filament → API／Action → Controller → Service → Model → DB。沿用既有資料表、狀態機與鎖；非必要不重構，不建新平台、Redis 系統、額外抽象層或第 46 個模組。

## 4. 測試與交付

依修改範圍執行 PHP、Pest、隔離 DB Migration、Flutter analyze/test/build、API 權限、交易併發、MEDIA 離線／下載／18 MiB 邊界測試；Android 實機有環境才執行。結果只能標 `PASS`／`FAIL`／`NOT RUN`／`BLOCKED`；**已寫程式不等於已驗收**。

**禁止自行 Git Commit／PR／Merge、正式部署、操作正式 DB。** 不要每個小問題交付一份 ZIP。

最終**一次交付**：①完整程式 ZIP；②檔案增刪改清單；③A01–A15 證據；④整批需求完成對照；⑤測試與未完成清單；⑥更新後完整 HTML（保留 14 Tab、45 模組卡）；⑦Page Atlas 與 `docs/PROGRESS.md`。未驗證功能一律標 `IMPLEMENTED / UNVERIFIED`。

**立即開始實際盤點與修正，不要只回覆計畫。**
