---
module_id: BOOK-03
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# BOOK-03｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

BOOK-03取消／改期／期限P0◐部分實作

功能
- 取消（v2）：L1→L2→L3→booking→charge；釋位後另開交易遞補
- 期限：cancel_closes_at、booking_closes_at、繳費期限（v2）
- 缺口：RescheduleSession 跨層補強（教室、人員、會員衝突；期限相容；已簽到不可改；成功後通知並排入 Google 同步）

核心流程改期請求 → 鎖場次 → 依序檢查 → 更新 → 通知 → 背景同步
資料表calendar_events、bookings
APIPOST /me/bookings/{id}/cancel
Filament 後台場次修改（改走 RescheduleSession）
Flutter App取消按鈕
規則、去重改期不得只改欄位
舊編號V1-M04、M05

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
