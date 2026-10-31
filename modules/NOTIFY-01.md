---
module_id: NOTIFY-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# NOTIFY-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

NOTIFY-01輕量通知中心P0◑批次 A：Claude 回報、待原始碼審查

功能
- notification_outbox（dedupe_key 唯一，與業務同交易寫入）→ 每分鐘派送（SKIP LOCKED、最多 5 次退避重試）
- 站內通知 notifications＋未讀數、分頁、全部已讀
- Email（Mail）；課前 48、24、3 小時與付款 24、3 小時提醒
- 缺口：管理員通知（依角色收件：退款審核、檢舉、改期）；data 統一帶 target_type／target_id 以便深連結
- V1 驗收範圍：重要通知（例如活動取消）在 P0 保證站內＋Email；手機即時推播屬 NOTIFY-02（P1）。若 V1 要求手機推播，需要你調整 NOTIFY-02 優先級

核心流程業務 Action → 同交易寫 outbox → COMMIT → 派送 → 站內／Email（之後加 FCM）
資料表notification_outbox、notifications
API/me/notifications、/me/notifications/{id}/read、/read-all
Filament 後台—
Flutter App通知列表（載入更多）、未讀徽章
規則、去重去重：不建第三套通知權威表；不建事件匯流排或 DSL（r4 §6.3）
舊編號舊M04、V1-M11

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
