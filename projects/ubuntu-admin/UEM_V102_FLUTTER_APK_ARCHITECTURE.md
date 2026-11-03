# Ubuntu UEM v1.02｜個人 Flutter APK 與部署規劃（2026-11-03）

狀態：**架構決策已確認，尚未部署**。完整設計參考 `Ubuntu_UEM_v1.02_Flutter_APK_Cloudflare_Deployment_Plan.html`（對話交付版，尚未納入 Git）。

## 決策
- Flutter Android APK 個人側載，不上架 Google Play；原 WebApp 繼續維持。
- 同一套 PHP API、SQLite WAL、Python Worker 與任務佇列；不得另建 Android CLI 執行器或重複 Job 表。
- 使用 Cloudflare Tunnel/Access 保護外網入口；不得直接暴露 origin、SSH、Docker Socket 或任意 Shell。
- 手機首次以 Email OTP 或 TOTP（Google Authenticator 相容）驗證後註冊裝置。**裝置授權預設 400 天（至少一年）**，到期重新驗證；短效 API Access Token 自動刷新。
- 400 天的裝置授權不等於 Cloudflare Access Session 400 天。採適用於原生 App 的 Access 登入／策略，不在 APK 內嵌全裝置共用的長期 Cloudflare Service Token。
- 長期裝置憑證應於首次註冊後產生、以 Android Keystore 保護的安全儲存方式保存；後端只存雜湊／公鑰，具裝置識別、撤銷、輪替、遺失處理、限流與稽核。APK 反編譯不能取出固定的伺服器密鑰。
- 高風險操作仍要求伺服器授權及再次確認；Read Only / Operator / Super 權限必須與 WebApp 共用。
- 原始 stdout/stderr 可查看與複製，對外僅 `.md` 匯出，下載必須驗證授權及記錄稽核。

## 現況與驗收邊界
- 已實測：PHP Security 42/42、UTF-8 2/2、SQLite Migration、PHP/Python WAL 跨容器寫入。
- 已實測：6 PHP submitters 建立 90 runs，4 Python claimers 全部唯一領取並完成；running 上限 1；無殘留鎖；Integrity/WAL/FK PASS；退出碼皆 0。
- 尚未驗收：HTTP 真實授權與 CSRF、裝置註冊/刷新/撤銷、Cloudflare Access、Android APK、UEM 真實執行與完整 Web UI 回歸。

## 開發順序與安全邊界
1. 在隔離測試環境驗收現有 HTTP API；先確認原有 `AccessAuth`、`Api`、`Service`、DB schema，禁止跳過現有授權。
2. 定義 API v1 契約及身分驗證 Adapter：Web session/CSRF 與 App Bearer Token **分流認證，匯流至同一授權服務**；不得讓 Bearer Token 免除角色檢查。
3. 建立裝置註冊、TOTP/Email OTP、400 天授權、短效 Token、撤銷/輪替、Audit、Idempotency-Key 的 Migration 和測試；不得將明文長期 Token 存在資料庫。
4. 實作 Flutter APK MVP：狀態、任務、日誌、`.md` 匯出、裝置管理；僅使用 Android 安全儲存，不使用硬編碼密鑰。
5. 在獨立 hostname 設定 Cloudflare Access/Tunnel，先測試拒絕未授權，再使用 Android 行動網路驗收。
6. APK 使用固定簽章金鑰自行側載更新；保留上一版 APK、API 部署版本與資料庫備份；確認不影響 iStudy/EachCore 正式服務。

## 禁止事項
- 不操作正式資料庫、不改動現有 Cloudflare 正式路由、不自動合併 PR。
- 不在正式 APK 中寫死長期共用密鑰、不開放任意 Shell/sudo/Docker Socket API。
- 90 筆 Claim 測試通過不等於完整 API/Worker/Flutter E2E 完成。
