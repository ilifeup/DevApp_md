---
module_id: BOOK-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# BOOK-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

BOOK-01單人預約引擎P0✔v2 已實作（未實測）

功能
- v2 已做：CreateBooking（L1→L2→L3、client_request_id 冪等＋唯一鍵、TIME_CONFLICT、名額池、欠費限制）
- 新增：BookingPrincipalResolver（預設只允許本人；FAMILY-01 啟用時才注入代理）
- 新增：選用的認證要求（CERTIFICATION_REQUIRED）、報名問卷同交易（FORM-01）

核心流程資格 → 冪等 → 鎖容量列 → 鎖會員列 → 衝突與欠費 → 確認或候補 → 帳單 → outbox → COMMIT
資料表bookings、member_states
API/series/{id}/bookings、/events/{id}/bookings、/me/bookings
Filament 後台預約列表、管理員取消
Flutter App詳情預約、我的預約
規則、去重一人一席；series 不拆成每堂 booking
舊編號舊M08、V1-M05

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
