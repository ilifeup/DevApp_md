---
module_id: CORE-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-01身份／登入／TokenP0◑批次 A：Claude 回報、待原始碼審查

功能
- Email 或手機註冊、登入；Sanctum opaque token（每台裝置一個）
- OTP 驗證手機與 Email（只存 HMAC 雜湊、60 秒冷卻、每小時 5 次、最多 5 次嘗試）
- 忘記密碼（回應一律中性，不洩漏帳號是否存在）、重設後撤銷全部 token
- 停權（is_active=false）→ 登入回 ACCOUNT_DISABLED；缺口：停權時要同步撤銷既有 token

核心流程註冊 → OTP → 管理員審核 verified_status → 登入 → Token → 每次 API 都由 Policy 判定
資料表users、personal_access_tokens、otp_codes、member_states
API/auth/register、/auth/login、/auth/logout、/auth/password/*、/me、/me/otp/*
Filament 後台會員：基本資料、啟用／停用、會員資格（必填原因）
Flutter App登入、註冊、忘記密碼、OTP
規則、去重verified_status=2 是預約資格的唯一權威（MEMBER-001）；家庭帳號（is_family_user）不能登入
舊編號舊M01、V1-M01

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
