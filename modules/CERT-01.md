---
module_id: CERT-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CERT-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CERT-01附加認證／徽章P1○未開始

功能
- certification_types、member_certifications（申請 → 審核 → 有效期限 → 撤銷），證件附檔不重新編碼
- badges（manual、attendance_count、certification）、user_badges
- 選用：series 或 calendar_events 設 required_certification_type_id 時，預約和遞補才檢查

核心流程申請 → 管理員核准 → 頒發徽章 → 到期或撤銷
資料表certification_types、member_certifications、badges、user_badges
API/certification-types、/me/certifications、/me/badges
Filament 後台審核佇列、徽章
Flutter App我的認證、徽章牆
規則、去重不取代 verified_status
舊編號舊M03 延伸、V1-M20

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
