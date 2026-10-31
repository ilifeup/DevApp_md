---
module_id: COURSE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# COURSE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

COURSE-01課程管理P0✔v2 已實作（未實測）

功能
- v2 已做：整期 series＋課堂（每週規則產生 N 堂草稿）、單堂 calendar_events（kind=lesson）、發布、修改、取消、指派講師
- 新增：offering_type=course 欄位，booking_mode（series 或 single）與類型分離
- 缺口：RescheduleSession（範圍：這次、此次及未來、全部；檢查教室、講師、已確認會員衝突；已開始或已簽到不可改）

核心流程建立課程 → 期別與堂次 → 發布 → 會員預約（整期一筆）→ 改期與通知
資料表series、calendar_events（+offering_type）
API/catalog、/series/{id}、/events/{id}
Filament 後台整期、場次
Flutter App目錄、詳情
規則、去重整期只有一筆 booking，出席按每堂記錄
舊編號V1-M04

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
