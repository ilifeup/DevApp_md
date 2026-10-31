---
module_id: EVENT-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# EVENT-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

EVENT-01活動管理P0✔v2 已實作（未實測）

功能
- v2 已做：單場活動、系列活動（series kind=activity）
- 新增：offering_type=event、reservation_required（不需報名的活動只展示、分享、上日曆）、資格（等級、認證）、簽到政策欄位

核心流程建立活動 → 場次 → 資格與名額 → 會員單人報名 → 異動或取消
資料表series、calendar_events（+offering_type、reservation_required、checkin_policy）
API同 COURSE-01
Filament 後台場次
Flutter App目錄、詳情
規則、去重永遠不開放訪客或多人預約
舊編號V1-M04

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
