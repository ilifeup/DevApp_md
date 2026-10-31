---
module_id: CAL-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CAL-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CAL-01App 日曆P0○未開始

功能
- 月、週、列表檢視：公開活動＋我的已確認報名（整期展開到每堂、只算有效補課）＋VIP 私人行程
- VIP 私人行程：等級由後台設定；可設每週或每月重複；可選「這次、此次及未來、全部」；VIP 到期後唯讀
- 顯示時區 Asia/Taipei（建議固定）

核心流程CalendarAggregator（日期區間）→ 合併各來源 → 加類型標籤 → App 顯示
資料表vip_events、calendar_recurrences、calendar_exceptions（新）
API/me/calendar?from=&to=、/me/vip-events
Filament 後台—
Flutter App月曆、週、議程、VIP 行程
規則、去重管理員不能任意查看私人行程
舊編號舊M09、S9

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
