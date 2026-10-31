---
module_id: REPORT-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# REPORT-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

REPORT-01統計報表／CSVP2○未開始

功能
- 預約與滿席率、候補遞補率、出席與缺席率、收款、退款、淨額、欠費帳齡、會員成長、內容閱讀、表單統計
- 以 Asia/Taipei 切日；金額用 Money；串流輸出 CSV

核心流程權限 → 篩選 → 唯讀聚合 → 表格與匯出
資料表—（即時查詢＋索引）
API—
Filament 後台報表頁
Flutter App—
規則、去重不同業務的計數不混算
舊編號V1-M22

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
