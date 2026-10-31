---
module_id: TAG-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# TAG-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

TAG-01基礎分類／標籤P0○未開始

功能
- 統一分類樹 categories（scope：article、product、service、media、event）＋ categorizables（多對多）
- 約束：父子分類必須同一 scope；深度最多 3 層；categorizable_type 只能掛到 scope 對應的模組（例如 scope=media 只能掛影音）；API 一律帶 scope 查詢
- 刪除與停用：有子分類或仍被引用時不能刪除，只能隱藏；隱藏分類不出現在 App，但既有關聯保留；模組停用時該 scope 的分類一併不提供
- tags＋taggables（文章、影音、活動、課程、商品），與分類分離
- 每個 App 是獨立資料庫，分類不會跨 App 讀取

核心流程建立分類（同 scope）與標籤 → 關聯內容 → 搜尋篩選
資料表categories（scope, parent_id, depth, slug unique per scope）、categorizables、tags、taggables
API/categories?scope=、/tags
Filament 後台分類樹（依 scope 分頁）、標籤
Flutter App篩選條件
規則、去重去重：共用分類表，不等於混用分類；CATALOG-01 只負責商品與服務的分類歸屬，不處理排程、付款、預約
舊編號—

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
