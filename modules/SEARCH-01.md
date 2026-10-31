---
module_id: SEARCH-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# SEARCH-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

SEARCH-01搜尋與收藏P0◐部分實作

功能
- v2 已有：/catalog?q、/posts?q
- SearchRegistry：各模組註冊搜尋提供者，查詢結果都經 ACCESS-01 過濾
- 每頁 20 筆、可續載、可篩類型、標籤、日期、地點名稱；相關性、最新、最舊排序；按搜尋鍵才觸發
- 不儲存任何個人搜尋歷史（資料庫與 App 本機都不存）
- 收藏：每位會員上限 1,000 筆；下架或失權的項目保留不可開啟的佔位，不洩漏標題

核心流程輸入 → 搜尋 → 授權過濾 → 分組結果 → 收藏（交易內鎖會員列，檢查上限）
資料表favorites（新：user、type、id unique）
APIGET /search、GET/POST/DELETE /me/favorites
Filament 後台可搜尋類型設定（不能瀏覽會員收藏）
Flutter App搜尋頁、收藏按鈕、我的收藏
規則、去重D01–D30 逐條追溯列為批次 D 的第一件工作
舊編號舊M19（D01–D30）、V1-M16

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
