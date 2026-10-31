---
module_id: FILE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# FILE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

FILE-01通用附件管理P2○未開始

功能
- 維持 P2：文章 PDF 附件、會員證明文件、表單附件、通用檔案庫
- 直接沿用 MEDIA-01 在 P0 建立的 media_files 與 MediaStorageService，不重複建表
- 新增 media_links（附件與文章、表單、證件的關聯）、檔案庫管理介面
- 每次預覽或下載都重新授權；簽名網址只保證完整性與時效，不取代權限
- 內容偵測 MIME、副檔名白名單、配額、防路徑穿越、防 SVG 與 HTML 主動內容；證件與原始檔不重新編碼

核心流程上傳 → 授權 → 驗證 → 私有儲存（沿用 MediaStorageService）→ 每次存取都做 Policy 檢查 → 撤權後立即拒絕
資料表media_links（新）；沿用 media_files
APIPOST /files、GET /files/{id}
Filament 後台檔案庫（引用檢查）
Flutter App附件預覽
規則、去重不是 MEDIA-01 的前置條件：共用底層程式與資料表，不代表兩個業務模組綁定
舊編號舊M20、V1-M18

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
