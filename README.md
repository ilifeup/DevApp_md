# DevApp_md｜AI App 專案計畫書管理

本儲存庫保存 App 開發計畫的 Markdown 文件，供 ChatGPT Plus、Claude Pro 與其他 AI 查閱、比對、更新及追溯版本。

> **目前階段：A｜AI → GitHub Markdown。** 尚未啟用 B 階段（PHP Cron／MySQL／Web）。

## 開始閱讀

1. **[AGENTS.md](AGENTS.md)**：所有 AI 的共同操作規範；修改文件前先閱讀。
2. **[DevApp_md v1.0 架構計畫書](docs/reference/DevApp_md_v1.0.md)**：A 階段設計、精簡目錄、三版本、變更審核及導入步驟。這是**規劃參考**，不是取代已核准的正式模組需求。
3. 後續逐步建立 **PROJECT.md**（總覽）、**modules/**（每模組唯一正式規格）、**tracking/**（需求／實作／驗收三版本證據）、**changes/**（變更提案與核准紀錄）。

## 更新原則

- 正式模組規格以 `modules/<MODULE-ID>.md` 為準；不得將新收到的開發報告直接視為已核准需求。
- 一般進度更新須有來源；新增或刪除功能、變更業務規則、API、資料庫、權限、架構或推翻決議，須先經使用者核准。
- 需求規格版本、程式實作版本、測試驗收版本**分開管理**；沒有證據不能標示完成。
- 以 Git Commit／Diff 保留歷史；不需要 GitHub Actions。
- 若必要文件尚未建立，應明確指出缺漏，不得自行編造正式規格或驗收結果。

## 對 AI 的簡短指令

> 請先讀 AGENTS.md 及 docs/reference/DevApp_md_v1.0.md，然後讀取目標模組的正式規格及追蹤文件。收到新 MD 後先比對差異、列出衝突與待核准項目；未核准不得修改正式業務規則。只更新必要文件，提交後回報檔案清單與 Commit SHA。
