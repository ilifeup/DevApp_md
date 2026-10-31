---
module_id: CAL-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CAL-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CAL-02Google Calendar 單向同步P0○未開始

功能
- OAuth（最小 scope）；每位會員在每個 App 綁一個 Google 帳號；建立 App 專用日曆
- App → Google 單向背景同步；首次只同步現在與未來的事件
- 取消：取消報名或場次取消 → 刪除 Google 對應事件，並刪除 google_event_map
- 防覆蓋：每次來源異動遞增 source_version；同步工作只在 version ≥ 已同步版本時寫入，舊工作直接丟棄
- token 失效：invalid_grant 時標記 google_accounts.status=reauth_required，暫停同步並以站內通知提示重新連結
- 解除連結：保留 Google 端既有事件，之後不再更新或刪除
- VIP 私人行程到期：App 內轉唯讀，Google 端既有事件保留不再更新
- token 以 encrypted cast 儲存；Queue 冪等並退避重試；Google 失敗不回滾預約交易

核心流程綁定 → 交易提交後排入同步工作（附 source_version）→ 對應表 → 重試或標記重新授權 → 錯誤紀錄
資料表google_accounts（+status）、google_event_map（+synced_version）、calendar_sync_jobs
API/me/google/connect、/me/google/callback、DELETE /me/google
Filament 後台同步錯誤監看
Flutter AppGoogle 日曆綁定頁、重新連結提示
規則、去重ICS 只能當唯讀附加，不能取代同步；需要真實憑證才能實測（BLOCKED）
舊編號舊M09、S9

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
