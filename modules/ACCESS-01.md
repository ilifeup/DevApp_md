---
module_id: ACCESS-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# ACCESS-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

ACCESS-01頁面／列表／內容三級權限P0◑批次 A：Claude 回報、待原始碼審查

功能
- 三層：Page（App 頁面入口）→ List（分類或列表）→ Item（單一內容）；第 4 道是 entitlement（播放授權），不是第四層頁面
- access_rules：scope ＋ subject（guest、member、verified、level、badge、role、user）＋ effect（allow、deny）
- 預設拒絕：沒有任何規則時視為拒絕；只有明確設定 guest allow 的頁面或內容才給訪客
- deny 優先：同層同時命中 allow 與 deny（例如等級 allow、指定會員 deny）一律 deny
- 父層不可覆蓋：父層 deny 時，子層任何 allow（包含指定會員 allow）都不能突破；Deep Link、搜尋、分享、嵌入都經同一個 AccessPolicy
- 頁面公開但列表鎖住：API 只回傳該列表的名稱、鎖定狀態與解鎖條件說明，不回傳項目
- 撤銷：會員資格、等級或徽章撤銷後，下一次請求立即失效；短效播放授權到期不再續發；離線授權在重連檢查時撤銷
- 已套用（批次 A＋審查 P0-01 修正）：/posts 列表與詳情；目錄頁面 catalog → 單一內容 series／event：列表逐筆過濾，詳情、直接網址、課堂（依所屬整期）與預約使用同一判斷

核心流程請求 → AccessPolicy::check(page → list → item, user) → entitlement（影音）→ 允許或 403 → 不載入受保護資料
資料表access_rules（新）
APIGET /app/config 回傳 pages 可見性；各 API 內部呼叫
Filament 後台權限規則編輯器（預覽某會員的判定結果與命中規則）
Flutter App依 /app/config 顯示入口與鎖定狀態（伺服器仍會強制）
規則、去重不能先載入受保護資料再用 UI 遮住
舊編號—

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
