---
module_id: MARKETING-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# MARKETING-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

MARKETING-01行銷版位／宣傳活動P1○未開始

功能
- placements（位置代碼）、campaigns（期間、受眾＝ACCESS-01 規則、素材、目標連結）
- App 依位置取得版位；點擊事件交給 ANALYTICS-01

核心流程建立活動 → 指定版位、期間、受眾 → 發布 → 點擊統計
資料表placements、campaigns
API/placements/{code}
Filament 後台宣傳活動
Flutter App版位元件
規則、去重目標連結走 LINK-01 允許清單
舊編號舊M15

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
