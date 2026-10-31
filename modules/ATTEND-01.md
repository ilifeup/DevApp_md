---
module_id: ATTEND-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# ATTEND-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

ATTEND-01活動彈性簽到／簽退P1◐部分實作

功能
- v2 已做：活動 QR（60 秒輪替＋10 秒寬限、可設定前綴）、簽退、櫃台代簽
- 新增：地點 QR（多場共用）、工作人員掃描會員 QR、依活動設定的選用 GPS（地點 QR 簽退免 GPS）
- 簽到資料在保存期滿後去識別

核心流程掃碼 → 共享鎖場次 → 鎖定讀取有效預約 → 補課來源檢查 → 出席 → 簽退（簽退後不能自行重簽）
資料表attendances、calendar_events（+checkin_policy、geo）、locations（lat、lng）
APIPOST /checkins
Filament 後台簽到 QR 頁、地點 QR、代簽到
Flutter App掃描、我的 QR
規則、去重候補不能簽到
舊編號舊M11

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
