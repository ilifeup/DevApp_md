---
module_id: PAY-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# PAY-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

PAY-01預約現金收款／應收P0✔v2 已實作（未實測）

功能
- charges（每筆預約一張，status 與 refund_state 兩條軸）
- 付款期限公式 6.3（保留時數 72／38 小時，爽約減半，不晚於開課）
- RecordCashPayment（整數分、冪等鍵加請求指紋、只收全額）、ExtendDueDate、MarkOverdue、欠費計數器每日對帳

核心流程預約 → 帳單與期限 → 提醒 → 櫃台收現 → 已付款（若原為欠費，計數 −1）
資料表charges、payments、member_states
API/me/charges、/me/charges/{id}
Filament 後台帳單、現金收款、延長期限、欠費清單
Flutter App帳單、收退款紀錄
規則、去重金額一律用 Money 以整數分計算
舊編號舊M12、V1-M07

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
