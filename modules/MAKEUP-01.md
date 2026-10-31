---
module_id: MAKEUP-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# MAKEUP-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

MAKEUP-01請假與補課P0✔v2 已實作（未實測）

功能
- 請假在開課前 2 小時截止、不限次數、不釋出整期名額；櫃台可代請假
- GrantMakeup（外部列 FOR SHARE）；每筆來源出席只能有一筆有效補課（唯一鍵）
- 有效性只透過 effectiveMakeups 判定；r4 新規則：原場次取消後，補課立即失效（待實作）

核心流程請假或缺席 → 管理員核准補課 → 指定場次 → 補課名額 → 簽到時重新驗證來源
資料表bookings（type=makeup）、attendances
API/me/bookings/{id}/leaves、/cancel
Filament 後台核准補課、代請假
Flutter App請假、補課失效標示
規則、去重補課不占一般名額
舊編號V1-M09

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
