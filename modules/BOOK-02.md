---
module_id: BOOK-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# BOOK-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

BOOK-02候補／遞補P0✔v2 已實作（未實測）

功能
- FIFO（waitlist_seq）、一筆交易只遞補一人、重新驗證資格、代管、欠費、時間衝突；不符則跳過並記錄 skip_reason
- 遞補給明確的繳費期限（promotion_grace 12 小時，不晚於開課；不足 30 分鐘時通知寫明剩餘分鐘）
- 開課後關閉候補（waitlist_closed）

核心流程釋位 → PromoteWaitlist（每分鐘排程補做）→ 確認並建立帳單 → 通知
資料表bookings（status=waitlisted）
API—（取消時自動觸發）
Filament 後台候補序號、跳過原因
Flutter App候補序號、結果
規則、去重去重：候補就是 bookings 的一種狀態，不另建 waitlist 表
舊編號V1-M06

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
