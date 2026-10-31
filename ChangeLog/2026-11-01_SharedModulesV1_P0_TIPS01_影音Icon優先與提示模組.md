# Shared Modules V1｜P0 TIPS-01 與影音操作原則修訂

狀態：使用者核准設計方向；**未代表程式已開發或驗收**。2026-11-01。

## 1. 最新影音 UI 原則（覆蓋先前「全部只能 Icon」的過度嚴格說法）

- **Icon 優先，文字其次**。按鈕數量越少越好；常見影音操作盡量使用清楚的 Icon，不強制所有按鈕絕對禁用文字。
- **影音 App UI 不得出現「下載」二字**；這是使用者已確認的產品介面限制。不要將此詞顯示在按鈕、提示、通知或錯誤訊息。
- 操作列優先維持「主要操作 Icon + 共用說明入口」，避免把播放、準備、重試、刪除等狀態全部做成同時可見的按鈕；依狀態顯示必要操作。
- 技術流程仍為完整加密檔取得、驗證、App 私有快取後本機播放；不得恢復伺服器音樂解密串流。
- 無障礙語意可以使用不含禁用詞的簡短描述；狀態或錯誤訊息保留必要資訊。

## 2. 新增 P0 共用模組 TIPS-01（第 46 個模組）

**用途**：會員按提示 Icon 時，在對應操作附近出現簡短小文字框，顯示後自動消失。由後台管理員設定每個 App／頁面／按鈕的說明與秒數。

**MVP**：
1. Flutter 共用提示元件：點擊問號／資訊 Icon 顯示簡短提示，預設 4 秒，允許 2–10 秒，點外部或再次點擊可關閉；不阻斷原功能、不全螢幕、不做多步驟教學。
2. Filament 後台：選 App、頁面、tip_key，編輯提示文字、秒數、啟用開關。簡易清單與編輯，不做 WYSIWYG 或多語管理平台。
3. 單一獨立資料表：`app_tips`（`id`, `app_id`, `page_key`, `tip_key`, `content`, `duration_seconds`, `enabled`, timestamps）；以 `app_id + tip_key` 唯一，後端限制長度、秒數及權限；只允許已知 App 與已註冊的 tip_key，避免任意注入提示。
4. API 僅提供 App 讀取該 App 已啟用提示；管理 API 由 Filament 處理。App 可快取已讀提示，離線時使用快取或內建提示；無設定不阻擋操作。
5. 提示元件須兼容手機窄螢幕、安全區、鍵盤與可捲動畫面，避免遮住目標按鈕。採用 Flutter 原生 Tooltip／Overlay，**不新增第三方 coach-mark 套件**。

**驗收**：三 App 分別能顯示各自設定；修改後重新取得生效；秒數與啟用開關正確；沒有設定不出錯；不阻擋主要操作；無越權修改；影音提示不出現禁用詞。

**禁止過度設計**：不做完整導覽流程、聚光燈動畫、點擊熱圖、觀看紀錄、A/B 測試、進階排程、AI 自動生成提示或多個相同功能的資料表。

## 3. 本批開發關係

- TIPS-01 為 **P0 新增共用模組**，優先納入本次設計開發；F 批次原已核准的 10 模組仍保留，不因此刪減。
- 正式模組數由 45 更新為 **46（原 45 + TIPS-01）**；歷史 r7.2/r7.3 原始 HTML 仍保留 45 張來源卡，下一版計畫書應新增 TIPS-01 模組卡，不得擅改歷史檔。
- r7.3 修正成果仍須獨立審查，不能因規格更新宣稱程式 PASS。
- 開發前先核對現有 Flutter 提示元件、Filament 與資料表；若已有等價功能，應重用或最小調整，避免重複造輪子。
- 原先 [Icon-only 舊決議](2026-11-01_SharedModulesV1_批次F十模組核准與影音Icon最高原則.md) 仍作歷史記錄；本檔最新 UI 原則優先。

## 4. 公開研究依據

- GitHub: https://github.com/RafaelBarbosatec/tutorial_coach_mark （較完整的 Coach Mark 導覽；本 MVP 不採用）
- Reddit: https://www.reddit.com/r/FlutterDev/comments/1pnux5f/best_flutter_package_for_feature_showcase/ （社群比較導覽套件）
- Reddit: https://www.reddit.com/r/flutterhelp/comments/1vwoat4/i_built_a_flutter_onboarding_package_where_the/ （提示定位與畫面變動問題）
