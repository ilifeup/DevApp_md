---
module_id: PAYGATE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# PAYGATE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

PAYGATE-01簡易 MVP 金流串接P0◐部分實作

功能
- v2 已有：PaymentGateway 介面、CashGateway、payment_attempts、payment_webhook_events、/webhooks/payments/{provider}
- P0 必須完成：Provider 介面、付款交易編號與狀態、冪等防重複付款、Webhook 驗簽介面、FakeProvider 模擬付款測試
- 未選金流商前不做：綠界或藍新正式串接、真實信用卡交易、正式退款 API、商家憑證設定、正式上線驗收
- 狀態標示：完成介面時只能寫「金流介面已實作」，不能寫「可正式收款」

核心流程建立 attempt → 導向或 SDK → 回調（驗簽、去重）→ 只由伺服器記入 payments
資料表payment_attempts、payment_webhook_events、payments
API/me/charges/{id}/payment-options、/payment-attempts、/webhooks/payments/{provider}
Filament 後台金流商設定
Flutter App付款方式動態顯示
規則、去重App 回傳的「付款成功」一律不採信（PAYMENT-001）
舊編號V1 6.10

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
