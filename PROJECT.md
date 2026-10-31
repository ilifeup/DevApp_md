# PROJECT.md｜DevApp_md A 階段總計畫

> 狀態：A 階段文件架構已建立；MEDIA-01 內容待正式來源核對。  
> 計畫依據：[DevApp_md v1.0 參考計畫](docs/reference/DevApp_md_v1.0.md)  
> AI 操作規範：[AGENTS.md](AGENTS.md)

## 1. 專案定位與目標
以 GitHub Markdown 作為 App 開發需求、變更決議、實作及驗收證據的共同管理入口，供 ChatGPT Plus、Claude Pro 及其他 AI 使用。避免 AI 擅自新增功能、推翻既有決議或將開發回報誤認為已驗收。

## 2. A 階段範圍與不做的事
**流程：** ChatGPT Plus／Claude Pro → 已授權 GitHub 工具或 API → GitHub Markdown。

本階段僅整理及維護文件；不啟動 PHP Cron、MySQL、Web、GitHub Actions、自訂 MCP Server 或自建 Agent；不將其他程式儲存庫的原始碼複製進來。

## 3. 技術與文件架構
- `AGENTS.md`：所有 AI 共用最高執行規則。
- `README.md`：專案入口及文件索引。
- `PROJECT.md`：總目標、範圍、架構、模組清單與路線。
- `modules/<ID>.md`：每個模組唯一正式規格；未經核准不得改寫需求。
- `changes/CHG-*.md`：來源差異、提案、決策及變更歷史。
- `tracking/<ID>.md`：規格、實作、驗收三版本及 Feedback。

Git Commit／Diff 負責文件歷史；需求版本與程式／測試版本仍獨立記錄。

## 4. 模組索引與相依
| 模組 | 正式規格 | 三版本追蹤 | 現況 |
|---|---|---|---|
| MEDIA-01（影音中心） | [modules/MEDIA-01.md](modules/MEDIA-01.md) | [tracking/MEDIA-01.md](tracking/MEDIA-01.md) | 已建待核對草案；尚非完整核准規格 |

其餘模組待取得核准來源後逐批加入，不先建立空白檔。跨模組相依關係尚未取得可核對資料，不予推定。

## 5. 開發里程碑
- **P0 文件骨架**：AGENTS、README、PROJECT、MEDIA-01、tracking、首次變更紀錄。
- **P1 MEDIA-01 試跑**：提供正式來源與新 Claude MD；核對需求 ID、差異、衝突、核准、Git Commit 與三版本證據。
- **P2 擴充**：驗證流程穩定後再逐批納入其他模組。

## 6. 共用原則
一模組一正式規格；變更提案與正式規格分離；C 模式審核；三版本獨立；不以 AI 回報或 CI 綠燈推定驗收；不重複抄寫模組規格。

## 7. 待決策與風險
- MEDIA-01 完整正式需求文件、核准版本與來源 Commit 尚待提供或查證。
- MEDIA-01 的 API、DB、UI、驗收案例及程式 Commit 尚待來源確認。
- 目前建立的模組檔案是**待核對草案**，不得當成已完成或已驗收。
- 本次文件拆分記錄：[CHG-20261101-001](changes/CHG-20261101-001.md)。
