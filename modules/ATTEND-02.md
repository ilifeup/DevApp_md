---
module_id: ATTEND-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# ATTEND-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

ATTEND-02課程出席／缺席P0✔v2 已實作（未實測）

功能
- 逐堂出席；結束後 60 分鐘排程標記缺席（absence_closed_at 確保冪等）
- 管理員更正必填原因並寫入稽核；有有效補課引用時拒絕更正

核心流程結束 → MarkAbsences → 管理員更正
資料表attendances
API/me/bookings/{id}（sessions[].attendance）
Filament 後台出席紀錄、更正
Flutter App逐堂出席
規則、去重爽約次數＝status=absent
舊編號V1-M09

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
