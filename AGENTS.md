# AGENTS.md｜DevApp_md 共用 AI 執行規則

**適用：ChatGPT、Claude 與其他 AI。** 本檔是本儲存庫的單一共用 AI 指引，不另外維護 CLAUDE.md。不同 AI 工具不一定自動載入本檔；接手任務時必須明確讀取。

## 1. 先讀取，再修改

1. 讀本檔及 [README.md](README.md)。
2. 參考 [A 階段計畫書 v1.0](docs/reference/DevApp_md_v1.0.md)。
3. 讀取目標 `modules/<MODULE-ID>.md`、`tracking/<MODULE-ID>.md` 及相關 `changes/` 文件（若已存在）。
4. 如有來源 MD、Commit 或正式決議，先核對其來源、時間、需求 ID 和核准狀態。
5. 缺少正式文件或證據時要明說，不能自行補造已核准內容。

## 2. 文件權威與精簡原則

- `modules/<MODULE-ID>.md`：該模組**唯一正式需求規格**；每項需求應有穩定 ID。
- `tracking/<MODULE-ID>.md`：分別記錄需求規格、程式實作、測試驗收三版本與來源證據；不得互相取代。
- `changes/CHG-*.md`：提案、差異、核准狀態及歷史；未核准提案不得寫入正式規格。
- `PROJECT.md`：整體目標、架構、路線與索引；避免複製各模組詳細規格。
- `docs/reference/`：參考計畫；不得以草案推翻後續已核准決議。
- 不擅自增加重複文件、空白模組或額外 AI 規範檔。

## 3. 變更權限：C 模式

**可自動更新**：有可核對來源的一般開發進度、Commit／測試證據、錯字、索引與不改變語意的修正。

**必須先取得使用者明確核准**：新增／刪除功能、改變正式需求、API、資料表或資料庫契約、權限、架構、重要業務流程、既有正式決議，以及本檔核心規則。

不得自動合併 PR、部署正式環境或操作正式資料庫。GitHub 寫入權限不等於取得業務決策核准。

## 4. 新 MD 處理流程

1. 辨識來源、模組 ID、需求 ID、版本與證據。
2. 與目前正式規格及追蹤資料逐項比對。
3. 分類為 `AUTO_APPLY`、`REVIEW_REQUIRED`、`CONFLICT`、`NO_CHANGE`。
4. 重大變更先提出差異、風險與待核准事項，不得先寫入正式規格。
5. 僅修改必要文件，檢查交叉引用、狀態及版本一致性。
6. 盡量集中提交一次 Commit，回報實際修改檔案與 SHA；無寫入權限不得宣稱已提交。

## 5. 三版本與證據

- **需求規格**：規格版本、核准狀態、對應變更／Commit。
- **程式實作**：實作版本、程式儲存庫／Commit、涵蓋的需求 ID。
- **測試驗收**：驗證版本、測試案例、結果、時間及可查證證據。

版本號與狀態分開；CI 綠燈不代表功能驗收通過；AI 宣稱完成不代表實際程式已完成。無證據時標示「待確認」或「未驗證」。

## 6. A 階段界線

本階段僅管理 GitHub Markdown。**不啟動** PHP Cron、MySQL、Web 顯示、自訂 MCP Server、AI Agent 或 GitHub Actions。先以單一模組試跑，成功後再逐批擴充。

## 7. 影音 UI 最高原則（AV-UI-001，2026-11-01 核准）

- **所有 3 App 的影音操作只用 Icon，不用文字按鈕**；所有影音介面可見文字禁止「下載」。不要以「離線使用」等文字替代操作圖示。無障礙語意可用不含禁用詞的描述。
- 只改 UI，不改完整加密檔取得、驗證、私有快取後播放的流程；不得恢復音樂線上解密串流。
- 詳見 [正式決議](ChangeLog/2026-11-01_SharedModulesV1_批次F十模組核准與影音Icon最高原則.md)。此規則優先於舊版 r7.2/r7.3 相衝突的 UI 敘述。

## 8. 影音 UI 最新原則與 P0 提示模組（2026-11-01）

- 最新規則為 **Icon 優先、文字其次、按鈕精簡**；取代第 7 節「全部只用 Icon」的過度嚴格表述。影音 UI 可見文字仍禁止「下載」。
- 新增 **TIPS-01（P0）**：點擊共用提示 Icon 顯示數秒小文字框；Filament 管理各 App／按鈕提示與秒數。禁止做大型教學導覽平台。
- [最新正式決議](ChangeLog/2026-11-01_SharedModulesV1_P0_TIPS01_影音Icon優先與提示模組.md) 優先於先前相衝突的 Icon-only 敘述。

## 9. 最新正式規則：三 App 獨立 DB、Icon 設定、QA-01（2026-11-01）

- **共用程式碼與 Migration，三 App 各自部署／各自 DB／各自 Filament 後台實例**；TIPS-01 的 `app_tips`、QA-01 的 `faqs` 均為每 App DB 各一表，**不加 app_id、不共用中央 DB**。
- 影音操作 **Icon 優先、文字其次、按鈕精簡**；各 App 後台可從白名單更換 Icon ID，不允許任意 HTML/JS/SVG；影音 UI 可見文字仍禁止「下載」。
- 新增 **QA-01（P0）** 極簡 FAQ，連同 TIPS-01（P0）使正式模組數為 **47，P0 33**。本節及[最新 Claude 指令](ChangeLog/2026-11-01_SharedModulesV1_Claude_47模組_TIPS_QA_影音Icon_三App隔離_整批指令.md)優先於第 7/8 節相衝突的舊描述。

## 10. Repository confidentiality and terminology rule (2026-11-02, user approved)

- Do not commit or publish the following exact Chinese strings anywhere in this repository, including Markdown, filenames, examples, comments, generated documentation, test fixtures, and future AI output: `天帝教`, `無相`, `無相氣學`.
- When describing the relevant apps or organization, use neutral English identifiers such as `App-T`, `App-W`, `Tiandi`, `Wuxiang`, or generic module IDs, only where necessary. Prefer opaque identifiers when names are not needed. A SHA-256 digest may be used as an internal reference but is **not encryption or reliable secrecy** for low-entropy names; do not publish a lookup table that reveals the original names.
- Before any commit, scan **all tracked text files and filenames**, including hidden folders and generated content, for exact forbidden strings. Normalize Unicode to NFC before scanning, and scan new changes before merging. If matches exist, redact or replace the words without altering technical contracts, API fields, database keys, or approved business behavior; if changing a contract is unavoidable, stop and request approval.
- Do not put the forbidden Chinese strings into CI logs, issue titles, pull requests, commit messages, or GitHub search reports. When reporting findings, use generic labels such as `restricted-term-1/2/3` and file paths only.
- This policy applies to **future repository content and current branch cleanup**. Removing strings from current files does **not** erase past Git history, forks, clones, caches, or previously published artifacts. History rewrite is a separate destructive operation and requires explicit approval.
- A search of the default branch alone does not prove all branches are clean. Check target branches separately and state any scan limitations.
