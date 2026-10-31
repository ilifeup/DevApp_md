---
module_id: NOTIFY-02
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# NOTIFY-02｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

NOTIFY-02FCM 手機推播P1○未開始

功能
- device_tokens（既有）加 app_version、failed_at
- FcmSender：FCM HTTP v1＋google/auth 服務帳戶（已核准套件）
- notification_preferences（類別：booking、payment、reminder、content、system，系統類不可關）
- 無憑證時模組自動視為關閉；UNREGISTERED token 自動清除

核心流程outbox 派送 → 檢查偏好與模組 → FCM → App 點擊 → 依 target 深連結並重新授權
資料表device_tokens（擴充）、notification_preferences（新）
API/me/devices、/me/notification-preferences
Filament 後台推播統計、群發公告（admin、必填原因、每日上限）
Flutter Appfirebase_messaging 註冊、前景提示、通知設定頁
規則、去重推播只是加速，站內通知才是權威
舊編號舊M04 通道、V1-M19

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
