# iStudy Page Atlas｜導入與整合開發計畫

- 日期：2026-11-03
- 專案：iStudy dev.62（唯一正式開發路線）
- 狀態：**導入方向已由使用者確認；P0 第一版持續補強；未實機驗收**
- 與 Shared Modules `modules/PA-01.md` 為**不同專案實作**，僅參考功能設計，不共用正式規格與驗收結果。

## 1. 原始碼基線與來源

| 類別 | 位置／來源 | 原則 |
|---|---|---|
| 正式 Git main | `/home/media/projects/istudy` | 唯一正式程式基線；不直接覆蓋 |
| UI Preview 來源 | `/home/media/projects/istudy-dev62` | 沿用既有程式，不重寫 |
| 使用者匯出 ZIP | `istudy_dev62_UI_Preview_PageAtlas_P0_baseline.zip` | Git main + Preview 整合封存，217 檔，使用者提供 SHA-256 `6e73a2098b2c9b7893fc2a23afa10178cd5159a1466aa6ed43eabed711848043` |
| ChatGPT 工作區 | P0 第一版及後續補強 | 尚未同步 Ubuntu Git main；交付與實機驗收分開 |

使用者確認 dev.62 是唯一正式開發路線；不再假設 `feature/ui-preview-template-v1` 分支存在。Git main 原有 CI／安全腳本、`.gitleaks.toml`、`package-lock.json` 必須保留。

## 2. 既有 UI Preview

沿用 `scripts/preview.mjs`、`scripts/preview/bridge.js`、`scripts/preview/mock.mjs`、`UI-PREVIEW.md` 及 npm `dev:client`／`dev:server`。預定僅綁定 `127.0.0.1:18662`（Client）及 `127.0.0.1:18663`（Server）；原始碼使用 React 共用頁面、esbuild watch、Mock fixtures。

**安全硬條件**：Preview 不得連接正式 API、資料庫、Agent、正式教材或執行寫入。Mock 非 GET 拒絕，未知 API 回 501；Bridge 的主機操作必須被隔離；CSP 與 HTTP 安全閘門須實測。來源程式設計或單元測試不等於實際網路隔離驗收。

## 3. P0 已核准功能

1. Client／Server 永久頁碼、Registry 與唯一性驗證；頁碼不因頁名或路由變更而重用。
2. 頁面索引、搜尋、上一頁／下一頁、目前位置。
3. React 關鍵元件及來源檔案定位。
4. PHP API 與 DB／欄位對照：必須有原始碼證據；無法核實標記「待核對」。
5. Super 權限下的頁碼角標、管理索引與資訊顯示；避免洩露內部路徑。
6. **「複製 頁面摘要給 AI 檢查」**：包含頁碼、來源、元件、API/DB 核對狀態及版本／待確認事項。
7. Preview 隔離測試與 Client／Server 編譯、實際操作驗收。

第一版工作區曾報告 Client 15 頁、Server 19 頁，共 34 頁；正式驗收前需重新核對 Registry 與實際路由。

## 4. P1（P0 驗收後）

- 簡易 Mock 示意及跳轉／返回，不執行正式業務操作。
- `Mock / Page Info Text / PNG`：示意頁面、頁面資訊文字與 SVG／Canvas PNG；保留手動截圖備援。
- Registry 同步與驗證：優先 TypeScript／JSON 為權威，不直接移植 Laravel `page-atlas:sync`、Filament 或 Flutter 專用程式。
- 不建立大型像素級 UI 模擬器、不新增非必要套件；初期不照搬 Shared Modules 的 `ui_pages`、`ui_page_components` Migration。

## 5. 開發／整合順序

1. 以 Git main 為基線，在獨立工作區整合 dev.62 Preview，保留原 CI、安全檢查與鎖檔。
2. 以已產出的 P0 工作區成果為候選，集中核對差異；不直接覆蓋 Git main。
3. 核實 PHP 路由、DB Schema、React 頁碼定位及權限；修正不一致。
4. 執行 Node 測試、TypeScript、Client／Server 編譯、HTTP Mock 隔離、瀏覽器、剪貼簿驗收。
5. P0 驗收後才開始 P1；使用者要求時才統一打包 ZIP。
6. Git 合併、正式部署、資料庫操作均不得自動執行。

## 6. 驗收門檻

頁碼唯一與路由覆蓋、權限隔離、搜尋導航、AI 摘要可複製、API／DB 證據正確、未知 API 與寫入拒絕、無正式服務網路出口、esbuild SSE 不受安全閘門影響。**CI 綠燈、靜態測試或工作區 ZIP 都不等於實機驗收。**

進度與待驗證事項請見 [Page-Atlas-PROGRESS.md](Page-Atlas-PROGRESS.md)。
