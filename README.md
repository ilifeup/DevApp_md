# DevApp_md｜AI App 專案計畫書管理

**目前：A 階段（ChatGPT Plus／Claude Pro → GitHub Markdown）**。本儲存庫管理需求規格、變更與三版本證據，不存放 App 程式碼，也不啟動 PHP／MySQL／Web／GitHub Actions。

## 文件入口

| 文件 | 作用 |
|---|---|
| [AGENTS.md](AGENTS.md) | 所有 AI 的共同操作規範；修改前必讀 |
| [PROJECT.md](PROJECT.md) | 專案目標、MVP、架構、路線圖、模組索引 |
| [modules/MEDIA-01.md](modules/MEDIA-01.md) | MEDIA-01 需求規格；**目前為待核對草案，尚非完整正式核准規格** |
| [tracking/MEDIA-01.md](tracking/MEDIA-01.md) | MEDIA-01 需求／實作／驗收三版本及證據 |
| [changes/CHG-20261101-001.md](changes/CHG-20261101-001.md) | 本次拆分決議與變更歷史 |
| [docs/reference/DevApp_md_v1.0.md](docs/reference/DevApp_md_v1.0.md) | 原始 A 階段架構計畫；保留作參考，不取代正式規格 |

## AI 更新流程

1. 先讀 `AGENTS.md`、`PROJECT.md`，再讀目標模組、追蹤及相關變更紀錄。
2. 接收新 MD／開發報告／Commit，核對來源、需求 ID、版本與核准狀態。
3. 先分類差異：`AUTO_APPLY`、`REVIEW_REQUIRED`、`CONFLICT`、`NO_CHANGE`。
4. 有來源的一般進度可更新；新增／刪除功能、API、DB、權限、架構及正式規則變更須先經使用者核准。
5. 規格、實作、驗收三版本分開；沒有證據不得標示完成。
6. 只修改必要文件，集中 Commit，回報 SHA 與待決策事項。

## 現階段界線

MEDIA-01 目前只是用來驗證文件架構的第一個模組，正式需求、程式 Commit 及驗收資料尚待核對。其他模組在取得來源後逐批建立，不先產生空白檔案。

**AI 任務指令：** 請先閱讀 AGENTS.md 與 PROJECT.md，再對照目標模組及追蹤檔；收到新 MD 先分析差異與衝突，重大變更等使用者核准，完成後回報實際 Commit。
