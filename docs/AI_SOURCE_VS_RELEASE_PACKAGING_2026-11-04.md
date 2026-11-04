# 跨專案｜減少打包程式大小與正確組譯規範（C 混合方案）

> 核准日期：2026-11-04。適用 Claude、ChatGPT、其他 AI；本庫僅記錄規範，不執行編譯、部署或程式修改。對應 [AGENTS.md 第 12 節](../AGENTS.md)。

## 一、必須區分的交付物

| 交付物 | 目的 | 內容 | 時機 |
|---|---|---|---|
| **精簡原始碼 ZIP** | 開發、審查、交接、可重建 | 第一方原始碼、API、Agent、UI、Schema、migrations、測試、必要設定／腳本、核准的離線資源 | 每次需要交接時 |
| **Linux Server App `.deb`** | 安裝 Ubuntu 圖形管理 App | Electron／Chromium runtime＋已建置 renderer；**不是** PHP／PostgreSQL／Agent 後端 | 整合／發行時 |
| **Windows／Linux Client installer** | 提供客戶端安裝 | 各平台 runtime 與編譯成品 | 發行時，獨立交付 |
| **Docker／部署 bundle** | 部署後端 | 鏡像、設定、部署產物；視離線需求 | 實機部署時，獨立交付 |

## 二、AI／CI／Ubuntu 的責任分配

- **Claude／ChatGPT 雲端**：功能開發、程式差異、靜態檢查、型別、必要的局部 build 與隔離測試；**只為交接輸出精簡原始碼 ZIP**。不要為了產出 ZIP 就下載整套 Electron／Chromium 並反覆製作大型 `.deb`。雲端可以做必要測試，不能以「省算力」跳過品質關卡。
- **GitHub CI**：PR 型別檢查、必要的編譯與回歸測試照常；**正式安裝包建置**僅在明確手動發行、tag 或核准的驗證情境啟動。大型 artifact 不回塞原始碼 ZIP；CI 成功不能代替目標機驗收。
- **Ubuntu 實機（優先）**：核對來源與相依版本、編譯 Linux Server／Client、產生 `.deb`、檢查 SHA-256、安裝在隔離測試環境、API／DB／Agent／UI／備份／權限／回退驗收。Windows 原生 Client 安裝包應由 Windows 或核准 Runner 建置，不能強迫 Ubuntu 跨平台完成所有封裝。
- **正式部署**：僅在核准後進行；需要 `.deb` 的部署腳本先生成相符版本。禁止以舊版成品混用新版原始碼。

## 三、原始碼 ZIP 必留／排除／例外

| 類別 | 原則 |
|---|---|
| **必留** | Server／Client／Agent／Worker 原始碼、packages、API／路由／權限、Schema／migrations、建置／部署腳本、配置樣板、測試、必要圖示、規格文件、可重現相依版本鎖定檔 |
| **排除** | `dist/`、`node_modules/`、`build/`、`out/`、`*.deb`、`*.AppImage`、`*.msi`、`*.exe`（非必要原始依賴者）、快取、log、測試暫存、模型、備份、正式教材、Token／密鑰、真實 `.env` |
| **條件保留** | 截圖、示範素材、第三方離線套件，必須按是否影響重建／離線安裝決定，不可為了縮小 ZIP 盲目刪除 |
| **iSTUDY 必留例外** | `deploy/vendor/age-v1.2.1-linux-amd64.tar.gz`（約 5.19 MB）：離線 Agent 映像建置需要；保留並校驗 SHA-256 |

**注意**：不要全域排除 `vendor/`、`*.tar.gz` 或 `*.exe` 而誤刪真正必需的第三方工具；以依賴圖與重建實測為準。ZIP 6–9 MB 是案例參考，不是硬性上限。

## 四、iSTUDY Dev62／Dev63 盤點案例（不是此庫原始碼）

| 項目 | 盤點數據 |
|---|---|
| Dev62 ZIP | 約 102.05 MB，215 檔 |
| Dev63 ZIP | 約 8.99 MB，237 檔 |
| SHA-256 相同 | 197 |
| 共同路徑不同 | 4：`package.json`、`packages/ui/src/App.tsx`、`packages/ui/src/styles.css`、`server/public/index.php` |
| Dev62 獨有 | 14，全部在 `dist/` |
| Dev63 獨有 | 36，含 Page Atlas Lite 相關檔 |
| 最大容量來源 | `dist/istudy-server-app_1.0.0-dev.62_amd64.deb` 約 92.32 MB（約 88 MiB），主要 Electron／Chromium |

**結論**：Dev63 是可接續開發的**候選原始碼基線**，並非已通過 Ubuntu 實機驗收。Electron Server App 管理 GUI 仍有首次設定精靈、教材轉檔管理及備份／Log／UEM 檢視等未被 CLI／Web 完全取代的功能；不能把「ZIP 排除 `.deb`」理解為「可刪掉 Electron Server App」。不能直接將 localhost 管理 API 暴露到 LAN／Cloudflare Tunnel。

## 五、每次交接前的強制檢查

1. 確認專案根目錄、來源版本／快照與可重建工具鏈；正規化 ZIP 相對路徑。
2. 列出每個檔案路徑、大小及串流 SHA-256，核對核心程式／Schema／腳本與離線資源。
3. 與上一個候選版本分為：相同、同路徑不同、僅舊版、僅新版；**不能只比較 ZIP 大小**。
4. 確認沒有誤帶 `dist/`、安裝包、`node_modules/`、機密、資料庫 dump、正式教材、個資；大型檔案例外須說明必要性。
5. 附 manifest、ZIP SHA-256、排除與例外清單、重建步驟、已測／未測及阻塞事項。發行成品另外附各自 SHA-256、版本與來源。
6. 發現重大核心缺失就停止宣稱完整；需重建的檔案必須保有來源與可行的建置流程。若目標主機無法連網，需事先規劃 Electron／其他必要依賴的安全離線來源。
7. 不得把單次 GitHub CI 綠燈、靜態 ZIP 比對、沙盒測試視為 Ubuntu／Windows 正式驗收。

## 六、限制與決策

- 本規範**不要求改動**目前任何 CI、deploy script、API、資料庫、權限或正式環境。
- 需要修改既有流程、移除 Electron GUI、改變部署方式或對外開放管理 API 時，先另案核准。
- iSTUDY 的完整原始碼政策與盤點證據，請參考該專案的 `docs/ISTUDY_DEV_PACKAGING_POLICY_2026-11-04.md`、`docs/DEV62_DEV63_FILE_DIFF_AUDIT_2026-11-04.md`、`docs/DEV63_SERVER_APP_PACKAGING_REVIEW_2026-11-04.md`。
