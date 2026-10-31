---
module_id: LINK-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# LINK-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

LINK-01Deep Link／Universal LinkP0○未開始

功能
- Android App Links（/.well-known/assetlinks.json）與 iOS Universal Links（apple-app-site-association），內容由設定產生
- 目標允許清單：article、product、service、course、event、invite；拒絕任意 redirect
- App 啟動或登入後重新載入 Policy；父層被拒時不能用連結直接進入子頁

核心流程點擊連結 → App（未安裝則開安全落地頁）→ go_router 解析 → 登入 → ACCESS-01 檢查 → 目標頁或顯示「不可用」
資料表—（沿用 share_links）
APIGET /.well-known/*、GET /links/resolve?path=
Filament 後台網域與憑證指紋設定
Flutter Appgo_router deep link、app_links
規則、去重連結永遠不能繞過權限
舊編號舊M14、r4 CORE-05

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
