---
module_id: TIPS-01
priority: P0
spec_status: approved_design_pending_implementation
spec_version: 2026-11-01
---
# TIPS-01｜三 App 共用按鈕提示模組



**用途**：會員按提示 Icon 時，在對應操作附近出現簡短小文字框，顯示後自動消失。由後台管理員設定每個 App／頁面／按鈕的說明與秒數。

**MVP**：
1. Flutter 共用提示元件：點擊問號／資訊 Icon 顯示簡短提示，預設 4 秒，允許 2–10 秒，點外部或再次點擊可關閉；不阻斷原功能、不全螢幕、不做多步驟教學。
2. Filament 後台：選 App、頁面、tip_key，編輯提示文字、秒數、啟用開關。簡易清單與編輯，不做 WYSIWYG 或多語管理平台。
3. 單一獨立資料表：`app_tips`（`id`, `app_id`, `page_key`, `tip_key`, `content`, `duration_seconds`, `enabled`, timestamps）；以 `app_id + tip_key` 唯一，後端限制長度、秒數及權限；只允許已知 App 與已註冊的 tip_key，避免任意注入提示。
4. API 僅提供 App 讀取該 App 已啟用提示；管理 API 由 Filament 處理。App 可快取已讀提示，離線時使用快取或內建提示；無設定不阻擋操作。
5. 提示元件須兼容手機窄螢幕、安全區、鍵盤與可捲動畫面，避免遮住目標按鈕。採用 Flutter 原生 Tooltip／Overlay，**不新增第三方 coach-mark 套件**。

**驗收**：三 App 分別能顯示各自設定；修改後重新取得生效；秒數與啟用開關正確；沒有設定不出錯；不阻擋主要操作；無越權修改；影音提示不出現禁用詞。

**禁止過度設計**：不做完整導覽流程、聚光燈動畫、點擊熱圖、觀看紀錄、A/B 測試、進階排程、AI 自動生成提示或多個相同功能的資料表。



完整決議：[2026-11-01 P0 TIPS-01](../ChangeLog/2026-11-01_SharedModulesV1_P0_TIPS01_影音Icon優先與提示模組.md)。
