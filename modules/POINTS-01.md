---
module_id: POINTS-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# POINTS-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

POINTS-01簡易 MVP 會員點數P0○未開始

功能
- point_balances（每位會員一列，作為鎖定列，存放餘額快取）＋ point_ledger（整數點數、只新增；發放、扣抵、沖正）
- 餘額不可為負：扣抵在交易內鎖 point_balances 後檢查，並行扣點不會超扣（POINTS_INSUFFICIENT）
- 冪等：idempotency_key 唯一，重複請求回傳原結果，不重複發點
- 沖正：只能針對某筆流水做一次反向流水（reversal_of 唯一），不修改原流水
- 到期：expires_at 欄位預留，V1 不做到期扣點流程（標示為後續擴充）
- 不跨 App 共用；不預設與現金等值；訂單與金流抵扣只預留 API

核心流程管理員或規則發放 → 交易內鎖定餘額列 → 檢查非負 → 寫流水 → 更新餘額 → 稽核
資料表point_balances、point_ledger（+reversal_of unique）
API/me/points、/me/points/ledger
Filament 後台發放、扣除、沖正（必填原因）
Flutter App我的點數
規則、去重餘額＝Σ 流水，加入 integrity:check
舊編號—

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
