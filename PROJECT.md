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

## 7. 待解決差異

- r7.2 同時出現「45 張模組卡」與「刪除 SYNC-01 後剩 44 個正式模組」等描述；保留 45 張來源卡，不自行刪除任何模組，待使用者核准正式清單。
- 原始 HTML 內有 r7.1 標頭文字但新增 10/31 決議 Tab；保留來源原文，不擅自修改版本字樣。
- 10/31 指令涉及移除未授權音訊串流；不得誤刪加密檔下載、影片連結或本機播放器 seek。
