---
module_id: CORE-03
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-03｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-03會員個人資料／隱私P0◑批次 A：Claude 回報、待原始碼審查

功能
- 顯示名稱（v2 已有）
- 新增：公開暱稱 nickname（唯一、2–20 字），留言與按讚顯示目前暱稱
- 新增：隱私設定 user_privacy（按讚公開顯示名稱，預設隱藏）
- 頭像（沿用 MediaStorageService 私有儲存）、個資最小化、修改會寫入稽核

核心流程會員修改 → FormRequest 驗證 → 授權寫入 → Audited 稽核
資料表users.nickname（新，unique）、user_privacy（新）
APIPATCH /me、GET/PUT /me/privacy、PUT /me/avatar
Filament 後台會員詳情（需 members.manage 才看真名）
Flutter App我的：暱稱、隱私、頭像
規則、去重管理員沒有權限時看不到會員真名
舊編號舊M01

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
