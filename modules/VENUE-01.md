---
module_id: VENUE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# VENUE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

VENUE-01地點／教室／人員P1✔v2 已實作（未實測）

功能
- v2 已做：locations、studios（預設座位）、staff（稱謂、技能）、event_staff 指派
- 缺口：教室時段重疊與人員時段衝突檢查（建立與改期時）

核心流程建檔 → 指派到場次 → 建立或改期時檢查資源衝突
資料表locations、studios、staff、event_staff
API—（詳情中帶出）
Filament 後台地點、教室、人員、指派人員
Flutter App詳情顯示地點與老師
規則、去重EachCore 預設關閉
舊編號V1-M03

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
