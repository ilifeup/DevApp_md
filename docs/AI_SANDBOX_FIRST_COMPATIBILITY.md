# AI Sandbox-First Compatibility Gate｜跨專案開發最高原則
> 2026-11-02 使用者指示；適用 ChatGPT、Claude 等 AI 協作專案。這是**技術選型流程**，不是要求所有專案統一使用 SQLite。
## 1. 順序：先驗證環境，再定技術，最後開發
1. 在正式寫主要程式之前列出：AI 沙盒的 OS、語言版本、現成 extension/driver、DB Server、CLI 工具、套件來源與網路限制。
2. 同時列出目標主機的實際版本、權限、部署條件、備份與長期維護需求。**沙盒可測優先，但不得犧牲正式執行環境安全或業務正確性。**
3. 選擇資料庫前，先以**最小跨語言實測**驗證 CRUD、Migration、transaction/rollback、unique constraint、並行領取、故障復原、實際權限；有證據才可定案。
4. 盡量優先使用沙盒內建或預先可用的技術；不可把「理論上可安裝」當作實際可用。套件來源遭政策封鎖時停止反覆安裝，找原生可測替代技術。
5. 對無法測試的核心路徑標記 `BLOCKED / UNVERIFIED`，不可把 syntax/lint/mock PASS 當成真實端到端驗收。
6. 技術選型需具備證據表：候選方案、依賴、沙盒實測、主機實測、資料一致性、安全/權限、備份、維護、移植風險、決策人與日期。
7. 確定**單一主要資料存取方案**再完整開發。不要同時維護多種未核准 DB adapter、不要先寫大批不可測的程式再補 DB。
8. 若原方案實際不可測，暫停擴大功能、做最小 PoC、呈報移植範圍與差異；架構/資料庫契約重大變更須依 `AGENTS.md` C 模式取得使用者核准。
9. 優先復用現有軟體、不額外安裝非必要套件；測試包含真實程序、多進程與失敗情境，最後仍需目標機驗收。整合修正後才集中輸出完整交付物。
## 2. 適用邊界
- 這是跨專案**流程規則**，不是自動改變 Shared Modules V1、Tiandi/Wuxiang/EachCore App、iStudy 等既有已核准架構。
- 個別系統的 DB 選型必須獨立決策；不得因 Ubuntu Admin WebApp 使用 SQLite 就把 Shared Modules 的獨立 MySQL/MariaDB 契約改成 SQLite。
- 任何正式機部署、正式資料庫修改、PR 合併仍需另外核准。
## 3. 本次案例
見 [Ubuntu Admin WebApp｜Claude 沙盒驗證與 SQLite 決策](Ubuntu_Admin_WebApp_SQLite_WAL_2026-11-02.md)。該項測試值來自 **Claude 提供的實測報告，非本庫獨立重跑**；正式 Ubuntu 驗收尚未進行。
