---
module_id: CONTENT-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CONTENT-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CONTENT-01文章／公告P0◐部分實作

功能
- v2 已有：posts（notice、article）、置頂、公開、發布時間、搜尋
- 狀態 draft、scheduled、published、unpublished、withdrawn、trashed；排程發布與到期下架
- RichEditor＋Markdown 匯入（Front Matter；同名更新只改標題與內文）；HTML 以 htmlpurifier 淨化
- 多分類最多 3 層、至少一類（共用 categories，見 TAG-01）
- 按讚（已驗證會員；公開名稱預設隱藏）、留言（黑名單即時公開，命中回 COMMENT_BLOCKED）
- 檢舉：同一輪 3 位不同會員 → 只觸發一次 AI 審核（keep、hide、unsure）；hide、unsure 或 AI 不可用時先自動隱藏，再通知管理員處理；AI 不能刪除

核心流程編輯 → 分類與可見性 → 發布 → 閱讀、按讚、留言 → 檢舉 → AI 審核 → 管理員結案
資料表posts（擴充）、post_likes、post_comments、comment_reports、comment_moderation_cases、post_views；分類與標籤用 TAG-01
API/posts、/posts/{id}、/posts/{id}/like、/posts/{id}/comments、/comments/{id}/reports
Filament 後台文章、留言案件佇列、黑名單、AI 設定
Flutter App分類分頁、內文、按讚、留言、檢舉、分享
規則、去重可見性一律由 ACCESS-01 判定，不另建 ArticleAccess
舊編號舊M05、M06、S8、V1-M15

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
