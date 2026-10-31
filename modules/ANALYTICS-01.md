---
module_id: ANALYTICS-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# ANALYTICS-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

ANALYTICS-01URL 點擊分析P1○未開始

功能
- click_events（安全事件：target、session hash、時間；不記錄個資）、去重、每日彙總

核心流程點擊 → /r/{code} 安全轉址 → 記錄 → 彙總
資料表click_events、click_daily
APIGET /r/{code}
Filament 後台點擊報表
Flutter App—
規則、去重文章瀏覽、按讚不等於點擊，不混算
舊編號舊M17

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
