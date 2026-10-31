---
module_id: CORE-06
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-06｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-06必要稽核P0◑批次 A：Claude 回報、待原始碼審查

功能
- audit_events 只新增：event、actor_id、subject、before／after、reason、request_id
- Audited trait 記錄參考資料增刪改；Actions 記錄業務事件
- 缺口：稽核篩選與 CSV 匯出、敏感欄位遮蔽清單、保留期滿後去識別

核心流程重要操作 → Audit::record（同一交易）→ 管理員以權限查詢與匯出
資料表audit_events
API—（只在後台）
Filament 後台稽核紀錄（篩選、前後差異、匯出）
Flutter App—
規則、去重不得修改或刪除稽核；不得記錄密碼、token、OTP
舊編號舊M07、V1-M13

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
