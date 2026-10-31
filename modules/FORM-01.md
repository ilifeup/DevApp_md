---
module_id: FORM-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# FORM-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

FORM-01問卷／動態表單P1○未開始

功能
- forms、form_versions（發布後不能修改）、form_submissions（一人一次用 generated column 唯一鍵）
- 欄位：文字、多行、數字、單選、多選、日期、勾選、檔案
- 報名問卷與 CreateBooking 同一交易；匿名問卷不能反推作答者

核心流程建立 → 發布版本 → 填寫（伺服器依 schema 驗證）→ 統計與 CSV
資料表forms、form_versions、form_submissions
API/forms/{id}、/forms/{id}/submissions
Filament 後台表單建立器、結果、匯出
Flutter AppDynamicForm
規則、去重—
舊編號舊M10、V1-M21

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
