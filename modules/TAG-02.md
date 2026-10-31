---
module_id: TAG-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# TAG-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

TAG-02進階標籤智慧P1○未開始

功能
- 標籤候選（從標題與內文擷取關鍵字）、合併與別名、相關內容推薦（共同標籤數）

核心流程候選 → 管理員確認 → 合併 → 推薦
資料表tag_aliases、tag_candidates
API/posts/{id}/related
Filament 後台標籤合併工具
Flutter App相關內容
規則、去重不自動上線未經確認的標籤
舊編號舊M16

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
