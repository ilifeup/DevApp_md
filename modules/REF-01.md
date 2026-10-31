---
module_id: REF-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# REF-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

REF-01Referral Friend 推薦好友P0○未開始

功能
- 每位會員在每個 App 有一組個人邀請碼與連結（複製或分享）
- 註冊時帶入邀請碼 → 驗證有效、同 App、不可自邀、不可重複歸因
- 會員看自己的邀請人數，管理員看總數；不預設獎金、點數或佣金

核心流程邀請 → 分享 → Deep Link → 註冊（referral_code）→ 同一交易建立 referrals → 統計
資料表referral_codes（user_id unique、code unique、status）、referrals（referred_user_id unique、referrer_user_id、accepted_at）
APIGET /me/referral、POST /auth/register（+referral_code）
Filament 後台邀請統計、停用邀請碼
Flutter App推薦好友頁
規則、去重獎勵屬於 REF-02（P2），不混入 REF-01
舊編號r4 CORE-06

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
