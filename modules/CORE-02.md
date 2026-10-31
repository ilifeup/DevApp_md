---
module_id: CORE-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-02角色／會員等級／權限P0◑批次 A：Claude 回報、待原始碼審查

功能
- 後台角色：user_roles（admin、staff），users 表沒有 role 欄位
- 新增：會員等級 member_levels（名稱、排序、is_vip），與角色、職稱分離
- 新增：role_permissions 權限矩陣（例如 reports.view、members.manage），Policy 依權限判定
- 特殊功能（例如 VIP 私人行程）依設定的等級判定，不寫死 ID

核心流程後台設定等級與角色 → 指派會員 → Policy 和 ACCESS-01 讀取 → 拒絕越權（deny 優先）
資料表user_roles（既有）、member_levels（新）、users.member_level_id（新）、role_permissions（新）
API/me（回傳 level）、/membership（狀態查詢）
Filament 後台會員：等級、角色、權限矩陣頁
Flutter App我的：會員等級標示
規則、去重與 FAMILY-01 實體隔離：不共用 Controller、Policy、Action、狀態機（r4 §5）
舊編號舊M02、V1-M02（不含家庭）

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
