---
module_id: CORE-05
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-05｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-05共用 Flutter UIP0◑批次 A：已建立程式結構，待 Flutter SDK 驗證

功能
- 已有：StatusChip、AsyncView、ErrorPanel、askText、InfoRow、RemoteConfig
- 拆分：packages/shared_core（ApiClient、ApiError、TokenStore、format、RemoteConfig）與 packages/shared_ui（元件、主題、PagedListView、EmptyState、DynamicForm、MediaThumb、MonthCalendar、SearchField）
- App Shell 依 enabled_modules 動態組裝底部導覽

核心流程App 啟動 → 讀 /app/config → 主題與標籤 → 依模組載入頁面 → 統一錯誤與空狀態
資料表—
API—
Filament 後台—
Flutter App全部畫面
規則、去重狀態一律文字＋圖示＋框線，不只靠顏色；最小點擊區 48dp
舊編號V1-M24

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
