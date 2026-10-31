---
module_id: SHOP-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# SHOP-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

SHOP-01電商商品／訂單整合邊界P1○未開始

功能
- WooCommerce Adapter 介面：商品與訂單唯讀映射、會員對應、Webhook 預留
- WooCommerce 保留商品、訂單、付款的權威

核心流程Woo 商品 → catalog_items.provider_ref → App 展示 → 前往 Woo 結帳
資料表catalog_items（kind=physical, provider=woo）
API/catalog-items（唯讀）
Filament 後台Woo 連線設定
Flutter App商品展示
規則、去重不建第二套訂單或付款權威；EachCore 電商 MVP 是獨立交付：商品、購物車、訂單、結帳有自己的交付需求（WooCommerce 為權威），依賴 SHOP-01（P1）；Shared Modules P0 完成不代表 EachCore 電商 App 完成
舊編號—

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
