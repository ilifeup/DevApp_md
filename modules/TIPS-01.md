---
module_id: TIPS-01
priority: P0
spec_status: approved_pending_implementation
spec_version: 2026-11-01
---
# TIPS-01｜共用按鈕短提示

- **共用程式碼，三 App 各自獨立資料庫各有一張 `app_tips` 表**，不共用 DB、不需 `app_id` 欄位。
- Flutter：點提示 Icon，在操作旁顯示短文字框，預設 4 秒，後台可設定 2～10 秒，自動消失／可關閉；不阻擋操作。
- Filament：各 App 管理員編輯已知 `page_key`／`tip_key` 對應純文字、秒數、啟停。
- `app_tips`：`id`, `page_key`, `tip_key`（唯一）, `content`, `duration_seconds`, `enabled`, timestamps。
- App 只讀取本 App 啟用提示，離線可使用快取或內建提示；不影響按鈕原功能。
- 不做多步驟導覽、統計、AI 提示、A/B、進階排程或第三方 coach mark 套件。
- 驗收：三 App 隔離、權限、秒數、停用、狹窄畫面與提示關閉；影音提示不得顯示禁用詞。

[Claude 完整整批指令](../ChangeLog/2026-11-01_SharedModulesV1_Claude_47模組_TIPS_QA_影音Icon_三App隔離_整批指令.md)。
