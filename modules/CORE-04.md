---
module_id: CORE-04
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# CORE-04｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。

CORE-04品牌／模組設定P0◑批次 A：Claude 回報、待原始碼審查

功能
- checkpoint 已做：config/modules.php＋settings.modules.*、ModuleRegistry（依賴檢查）、module:<key> middleware（404 MODULE_DISABLED）、AppResource::$module、SkipIfModuleDisabled job middleware、ModuleSettings 頁、Branding、GET /app/config
- 缺口：停用狀態機 ENABLED → DRAINING → DISABLED；停用前預檢未結的預約、候補、退款、簽到、排程
- DRAINING 結案規則：拒絕新預約與新入口；既有帳單仍可收款、退款與結案；已排入的通知照常送出，但不再產生新的提醒；後台的查詢、稽核、匯出保留；Scheduler 只執行結案類工作（逾期、缺席、退款提醒）
- DISABLED 後：資料表與歷史資料保留，後台以唯讀查詢與稽核；重新啟用前再做依賴檢查
- 交易邊界（審查 P0-02 修正）：業務 Action 交易第一步以共享鎖鎖住相關模組狀態列（L0 閘門）並在鎖內重驗；狀態變更以 FOR UPDATE 鎖同列後才統計未結項目，兩者不會交錯；帳務未結項目含進行中付款嘗試與未處理回調
- 三種操作：建立新業務＝只限啟用；既有業務結案＝啟用或停用中；本人歷史資料唯讀＝任何狀態（Policy 照常限本人）
- 安全結案（SPEC-02，程式強制）：帳務、出席、家庭設定 drain_after，在預約與目錄完成停用前一律停在停用中，取消已付款預約產生的退款審核一定落在仍可結案的帳務模組；相依模組未啟用只會讓本模組不能接新業務，不會關掉本模組既有項目的結案；不靠後台提示停用順序
- 缺口：通知文案覆寫、資料保留清除（retention:purge）、各 App 的 flavor 與 applicationId

核心流程Filament 設定 → 依賴與未結交易預檢 → 稽核 → /app/config 快取 60 秒 → Flutter 隱藏入口
資料表settings（既有，key 命名空間 modules.*、branding.*、labels.*、notify.templates.*、retention.*）
APIGET /app/config（免登入）
Filament 後台模組與品牌頁、停用預檢報告
Flutter AppRemoteConfig：標題、主題、模組可見性
規則、去重後端在路由、Action、Filament、Job、Scheduler 全層強制，不能只隱藏選單
舊編號V1-M23

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
