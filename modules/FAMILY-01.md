---
module_id: FAMILY-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# FAMILY-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

FAMILY-01家庭代管（獨立模組）P1◑批次 A：Claude 回報、待原始碼審查

功能
- v2 已有：family_links（pending、active、rejected、ended）、申請、審核、額度，以及代為預約、簽到
- 重構：獨立 Routes（/family/delegations）、Policy、Action；CreateBooking 改透過 BookingPrincipalResolver
- 關閉時：本人預約、角色、資格完全不受影響；代理 API 一律拒絕

核心流程管理者申請 → 管理員核准 → 代為預約（單人、成員有獨立 ID）→ 結束代管
資料表family_links
API/family/delegations（取代 /me/family）
Filament 後台家庭代管審核
Flutter App家庭成員頁、預約時選擇替誰預約
規則、去重EachCore 預設關閉；不得把家庭欄位放進 users 必填
舊編號V1-M02 中的家庭部分

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
