---
module_id: SERVICE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# SERVICE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

SERVICE-01其他預約服務P0◐部分實作

功能
- v2 已有：推拿時段（calendar_events kind=massage，名額＝當班人數）
- 新增：服務提供者（staff）、可預約時段範本（週期產生 slot）、一對一或多人容量、單次或系列；不強迫綁教室、補課或簽到

核心流程上架服務 → 產生時段 → 會員預約 → 鎖時段名額 → 收款與通知（依需要）→ 完成或取消
資料表service_slot_templates（新）、calendar_events（kind=service）
API同目錄 API
Filament 後台服務與時段產生
Flutter App服務預約
規則、去重不需預約的服務不會產生 booking
舊編號V1-M04（推拿時段）

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
