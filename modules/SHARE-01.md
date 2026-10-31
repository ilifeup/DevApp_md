---
module_id: SHARE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# SHARE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

SHARE-01基本分享P0○未開始

功能
- 文章、商品、服務、課程、活動、邀請都可分享
- 後端產生 HTTPS 分享網址 /s/{code}；Blade 落地頁輸出安全的 Open Graph 摘要
- 受限內容只顯示登入提示；下架或撤權後摘要不再提供

核心流程App 分享 → POST /share-links → 系統分享面板 → 朋友開啟 → 公開摘要或登入提示 → 登入後由 Policy 重新授權
資料表share_links（新：code unique、target_type、target_id、created_by、disabled_at）
APIPOST /share-links、GET /s/{code}（web）
Filament 後台分享連結停用
Flutter App分享按鈕（share_plus）
規則、去重OG metadata 不得洩漏受限內容
舊編號r4 CORE-04

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
