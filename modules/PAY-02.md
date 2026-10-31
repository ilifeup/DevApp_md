---
module_id: PAY-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# PAY-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

PAY-02取消退款／部分退款結案P0✔v2 已實作（未實測）

功能
- 已付款後取消 → refund_state=review（cancel_after_paid、paid_after_cancel、overpaid）
- DecideRefund：全額、部分、不退（必填原因）；RecordRefund 寫入負數流水
- F03 方案 A：partial → closed（剩餘不退，稽核記錄已退總額與不退餘額，結案後不能再退）

核心流程取消 → review → 管理員決定 → 登錄退款 → partial、done 或 closed
資料表charges、payments（append-only）
API—（只在後台）
Filament 後台退款決定、登錄退款、收退款流水
Flutter App退款狀態、已退金額
規則、去重Q18：不自動退款；退款失敗不寫入流水
舊編號V1-M08

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
