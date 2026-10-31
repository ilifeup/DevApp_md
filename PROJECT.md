# PROJECT.md｜Shared Modules V1 專案計畫總覽（r7.2 匯入）

> 來源：2026-10-31 r7.2 HTML（14 個 Tab、45 張模組卡）；**文件匯入不代表實作或驗收**。原始檔完整保留。此儲存庫只管理 Markdown 計畫書，與程式碼開發基線分離。

## 1. 專案目標與範圍

將 Shared Modules V1 的原始 r7.2 計畫書拆成可供 ChatGPT／Claude 迭代管理的文件。採用 [AGENTS.md](AGENTS.md) 的 C 模式；新來源先比較、正式業務規則修改須核准。

## 2. 正式來源與閱讀優先順序

1. [10/31 決議與 Claude 修正指令](changes/CHG-20261031-MEDIA-01.md)（在其適用範圍內優先於舊版衝突內容）。
2. [r7.2 原始完整 HTML](docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)（保留 14 Tab 與原文）。
3. [r7.2 45 張模組卡拆分](modules/)（文字轉換版本；遇歧義回查 HTML）。
4. [A 階段文件管理規劃](docs/reference/DevApp_md_v1.0.md)。

## 3. 技術架構、流程、資料庫及規則

r7.2 原始 HTML 的「技術與架構」「核心流程」「資料庫」「核心設計規則」「Page Atlas」等內容仍保留在完整原始檔。這些跨模組內容**尚未逐項重構為正式共用規格**，不得誤以為單一模組卡已包含所有跨模組契約。先不複製到多份文件，以免產生不同步的規則。

## 4. 模組索引（45 張原始卡）

- [PA-01](modules/PA-01.md)
- [CORE-01](modules/CORE-01.md)
- [CORE-02](modules/CORE-02.md)
- [CORE-03](modules/CORE-03.md)
- [CORE-04](modules/CORE-04.md)
- [CORE-05](modules/CORE-05.md)
- [CORE-06](modules/CORE-06.md)
- [NOTIFY-01](modules/NOTIFY-01.md)
- [NOTIFY-02](modules/NOTIFY-02.md)
- [ACCESS-01](modules/ACCESS-01.md)
- [SHARE-01](modules/SHARE-01.md)
- [LINK-01](modules/LINK-01.md)
- [REF-01](modules/REF-01.md)
- [CONTENT-01](modules/CONTENT-01.md)
- [SEARCH-01](modules/SEARCH-01.md)
- [MEDIA-01](modules/MEDIA-01.md)
- [FILE-01](modules/FILE-01.md)
- [TAG-01](modules/TAG-01.md)
- [CATALOG-01](modules/CATALOG-01.md)
- [VENUE-01](modules/VENUE-01.md)
- [COURSE-01](modules/COURSE-01.md)
- [EVENT-01](modules/EVENT-01.md)
- [SERVICE-01](modules/SERVICE-01.md)
- [BOOK-01](modules/BOOK-01.md)
- [BOOK-02](modules/BOOK-02.md)
- [BOOK-03](modules/BOOK-03.md)
- [FAMILY-01](modules/FAMILY-01.md)
- [PAY-01](modules/PAY-01.md)
- [PAY-02](modules/PAY-02.md)
- [PAYGATE-01](modules/PAYGATE-01.md)
- [POINTS-01](modules/POINTS-01.md)
- [ATTEND-01](modules/ATTEND-01.md)
- [ATTEND-02](modules/ATTEND-02.md)
- [MAKEUP-01](modules/MAKEUP-01.md)
- [CAL-01](modules/CAL-01.md)
- [CAL-02](modules/CAL-02.md)
- [CERT-01](modules/CERT-01.md)
- [FORM-01](modules/FORM-01.md)
- [REPORT-01](modules/REPORT-01.md)
- [MARKETING-01](modules/MARKETING-01.md)
- [TAG-02](modules/TAG-02.md)
- [ANALYTICS-01](modules/ANALYTICS-01.md)
- [SCORE-01](modules/SCORE-01.md)
- [REF-02](modules/REF-02.md)
- [SHOP-01](modules/SHOP-01.md)

## 5. 開發狀態與驗收

來源 HTML 表示第 1～4 批已有程式工作，但狀態為 **IMPLEMENTED / UNVERIFIED**。Pest、Migration、Flutter 分析／測試／建置、Android 實機等未驗收項目不得標為 PASS。DevApp_md 尚未獨立查驗程式庫及測試輸出。

## 6. 開發路線

- P0：完成 r7.2 來源封存、45 模組卡及 10/31 指令拆分。
- P1：逐項核對 MEDIA-01 正式決議、需求 ID、程式證據與測試證據。
- P2：核對跨模組規則、43+ 其他模組、來源版本與相依關係。
- B 階段 PHP／MySQL／Web 不在本次工作範圍。

## 7. 最新已確認決議與下一批次審查

- **模組數量固定為 45 個**；原 HTML 的「刪除 SYNC-01 後 44 個」屬過時描述，不再列為待決問題。
- **MEDIA-01 唯一音樂播放方式：完整下載加密檔、驗證並寫入手機加密快取後，才允許本機播放。** 禁止伺服器解密音樂線上串流及其專用 URL、路由、並發鎖、503 重試、HLS／Range、Flutter 線上播放按鈕；必須保留加密檔下載 `media.cipher`、離線授權、本機 seek 與影片連結。
- 10/31 決議規定離線授權最長 365 天、單曲上傳硬上限 18 MiB = 18,874,368 bytes；保留 AES-256-GCM／X25519／Ed25519。
- A01–A15 是**需在程式基線逐項確認**的審查清單；不存在的問題不得為了完成清單而修改。
- 下一批次必須先核對正式範圍、既有 ZIP 與 `docs/PROGRESS.md`，集中修正與測試，最後交付單一 ZIP；不得自行擴充、過度設計或擅自 Git Commit／PR／Merge。
- [完整 Claude 下一批次執行指令](ChangeLog/2026-11-01_SharedModulesV1_Claude_下一批次整合開發指令.md)。

## 8. 本次執行範圍裁定（2026-11-01）

- 使用者已明確選擇 Claude 提出的**方案 1：本次只交付修正**。MEDIA-01 錯誤串流清理及 A01～A15 修正由 Claude 回報已完成，但 DevApp_md **尚未取得 ZIP 與測試證據，不能標示已驗收**。
- 本次只完成測試準備、證據整理、單一完整 ZIP、HTML、Page Atlas、`docs/PROGRESS.md`，然後停止等待 ChatGPT 審查。
- **批次 F 不啟動**；`NOTIFY-02` 因 D4 待決、`SHOP-01` 因 EachCore 獨立交付邊界，不納入本次。其他 F 候選模組也不得自行開發。
- **批次 G 不啟動**；Ubuntu 整合驗收待另行核准，缺少 PHP 8.4／MySQL／Flutter 測試環境者標 `BLOCKED` 或 `NOT RUN`。
- 最新指令：[Claude 批次 1～2 修正封存交付](ChangeLog/2026-11-01_SharedModulesV1_Claude_批次1至2修正封存交付指令.md)。

## 9. 待解決差異

- 正式模組數量已由使用者確認為 **45**；原始 HTML 中「44」僅作為歷史來源保留，不再視為未決。
- 原始 HTML 內有 r7.1 標頭文字但新增 10/31 決議 Tab；保留來源原文，不擅自修改版本字樣。
- MEDIA-01 音樂串流移除方向已確認；**程式是否完成刪除仍待 Claude 檢查 ZIP 並交付測試證據**。

## 10. 最新決議：影音 Icon-only 與批次 F（2026-11-01）

- **最高原則 AV-UI-001**：3 App 影音操作只使用 Icon，不使用文字按鈕；影音 UI 所有可見文字禁用「下載」。維持完整加密檔準備後本機播放。
- **批次 F 全部 10 模組已核准**：FAMILY-01、VENUE-01、ATTEND-01、FORM-01、CERT-01、NOTIFY-02、SHOP-01、MARKETING-01、TAG-02、ANALYTICS-01。
- FAMILY-01 優先沿用既有程式並新增後台管理員模組開關；SHOP-01 三 App 可用，後台僅簡易 WooCommerce 設定；NOTIFY-02 沿用既有通知流程串 FCM。
- **不過度開發**：只做已核准的必要功能，現有功能不重寫，批次 F 一次整批開發。
- **r7.3 驗收與 F 規劃並行**，但前者未通過的測試不得標 PASS；批次 G 仍待安排。
- [完整正式決議與開發邊界](ChangeLog/2026-11-01_SharedModulesV1_批次F十模組核准與影音Icon最高原則.md)。先前「F 暫緩」記錄為當時的交付裁定，已由本次對下一批 F 的明確核准更新。

## 11. 最新 P0 TIPS-01 與影音 UI 修訂（2026-11-01）

- 影音 UI **Icon 優先、文字其次、按鈕精簡**，取代先前絕對 Icon-only 規則；影音 App 可見文字仍禁用「下載」。
- 新增 [TIPS-01](modules/TIPS-01.md) P0 三 App 共用按鈕提示模組：按提示 Icon 顯示可設定秒數的小文字框，Filament 後台可按 App／頁面／按鈕設定。**原生 Flutter、單一獨立資料表、MVP**。
- 正式模組數 **46（原 45 + TIPS-01）**；r7.2/r7.3 歷史來源仍為 45 卡，後續新版計畫書補上 TIPS-01。
- [最新完整決議](ChangeLog/2026-11-01_SharedModulesV1_P0_TIPS01_影音Icon優先與提示模組.md)；原 10 模組 F 批次照原核准範圍開發，r7.3 驗收仍獨立進行。
