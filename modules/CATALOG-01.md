---
module_id: CATALOG-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CATALOG-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CATALOG-01商品／服務目錄分類P0○未開始

功能
- catalog_items：kind（physical、service）、reservation_required、provider_ref（指向 series、calendar_events 或 WooCommerce 商品 ID）、visibility、share slug
- 商品與服務分類樹（categories scope=product、service）、排序、顯示設定
- 不處理預約、整期或單次、金流

核心流程後台建立目錄項 → 分類 → App 顯示 → 需預約時轉給 COURSE、EVENT、SERVICE 的提供者
資料表catalog_items（新）、categories
API/catalog-items、/catalog-items/{id}
Filament 後台目錄項與分類
Flutter App商品服務目錄
規則、去重不建萬用表，不搬 WooCommerce 的訂單資料
舊編號r4 CAT-01

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
