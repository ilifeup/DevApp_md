# 新對話接手｜Shared Modules V1 MVP（先讀懂，禁止直接開發）

> 此文件是新 AI 的**第一個閱讀入口**。本次接手第一階段只有閱讀、比對、彙整與回報；使用者確認之前**不得修改程式、文件、Docker、資料庫、PR 或 CI**。

## 1. 先完整閱讀（不可只看摘要）
1. [本庫 AGENTS.md](../../AGENTS.md)（文件權威、AI 行為規則）與 [README.md](../../README.md)。
2. [PROJECT.md](../../PROJECT.md)（最終專案目標、三 App 共用架構、階段界線）。
3. [正式模組規格](../../modules/)：依當前專案需求檢查，特別是 BOOK、ATTEND、NOTIFY、FORM、MEDIA、SHOP、CATALOG。
4. [最新核准決議 ChangeLog](../../ChangeLog/) 與 [changes/](../../changes/)；注意 2026-11-01 更新 **47 模組／P0 33／P1 10／P2 4**、TIPS-01、QA-01、三 App DB 隔離、MEDIA UI。
5. [原計畫 r7.2 來源](../reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)（歷史來源；衝突以最新核准決議為準）。
6. [Ubuntu 固定環境](../environment/ubuntu_shared_modules_mvp.md)（不用重複詢問已知版本、路徑、Docker）。
7. [Pest 測試安全](../testing/pest_database_safety.md)（必須保護 shared_pest_test）。
8. **[已知錯誤、根因與防止重犯](../testing/KNOWN_ISSUES_AND_PREVENTION.md)**（每次遇到相同症狀先查，已修問題不得從頭重做）。
9. [Ubuntu 實機 Debug 進度](ubuntu_mvp_debug_20261101.md)（最新 BatchF 20 PASS／11 FAIL）。
10. 本庫 [tracking/](../../tracking/) 以需求規格、程式實作、測試驗收三版本分開表示；未驗收不能標完成。

> 本 GitHub 分支 `docs/ubuntu-mvp-handoff-20261101` 保存以上新增交接文件；其他正式文件以該分支所繼承的原有資料及最新正式核准決議為準。DevApp_md **只管理文件，不是 Ubuntu MVP 程式碼本體**。

## 2. 閱讀完成，先向使用者提交「專案理解報告」
請分章回報，並區分【文件確認】【實機證據】【推論／待驗證】：

1. **專案最終目標**：Shared Modules 要解決什麼問題、三個 App 如何共用程式與獨立部署，MVP 範圍、47 模組優先級。
2. **架構與核心業務**：Flutter、Laravel 13、Filament、MySQL、API、會員、預約、候補、付款、簽到、媒體、權限與 Page Atlas 的關係。
3. **正式規則與決策優先順序**：各模組規格、最新 2026-11-01 決議、禁止自行擴展的邊界，衝突要列出。
4. **Ubuntu 本機環境**：Docker / Compose / PHP / MySQL / Composer / Laravel / Pest / Flutter 版本、兩份源碼位置、掛載與資料庫用途、iStudy 隔離。
5. **目前進度與證據**：25 migrations PASS；Unit 43 PASS；Schema 91 PASS；BatchF 20 PASS／11 FAIL；歷史 Feature 114 PASS／46 FAIL（不是當前狀態）；Concurrency NOT RUN；Flutter 未驗證。
6. **已知錯誤與防重犯**：先說明已解決的關鍵根因（DB/Guard、Forms scope、Query Builder whereKey 等），再分類 11 Fail 及完整 Feature 其他待查。指出禁止使用的假修復。
7. **後續驗收策略（只提出方案）**：一次集中查根因、修復與回歸的順序；未知點只指出真正會影響正確性者。
8. **理解的限制與矛盾**：哪些只有 TXT 時序證據、哪些後續日誌已取代、哪些需要本機再次確認；不得虛報已讀未讀內容。

## 3. 第一階段禁止操作
- 不得直接 Debug、生成補丁、執行命令、修改任何程式或 GitHub 文件。
- 不得刪除 DB、跳過 TestDatabaseGuard、碰 iStudy、停用安全規則或猜測正式需求。
- 不得自動提交 Commit／PR／Merge／部署／新增工具。
- 完成上述報告後**停止並等待使用者核對**，得到下一步明確要求再操作。

## 4. 第二階段開發的預備資訊（先記住，尚不得操作）
- 本機 backend：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend`。
- PHP 容器：`eachcore-dev-php-1`；Pest 唯一 DB：`shared_pest_test`；`shared_mvp_test` 只是驗收 DB，不能做 Pest。
- GitHub 舊 PoC `/home/media/GitDev/ShareModules` 和本機 ZIP MVP 不是相同程式基線，禁止直接覆蓋。
- 未獲得後續授權前不修改任何東西。

## 2026-11-02 交接必讀：最新回歸與排錯知識
1. [完整 Debug 根因索引／AI 防重犯手冊](../testing/DEBUG_PLAYBOOK_20261102.md)：Round1–11 根因、測試陷阱、不可重犯清單。
2. [Ubuntu 2026-11-02 已驗證環境](../environment/UBUNTU_MVP_VERIFIED_20261102.md)：版本、路徑、容器、DB Guard、備份、測試指令。
3. [已知錯誤舊索引](../testing/KNOWN_ISSUES_AND_PREVENTION.md) 與 [歷史 Debug 交接](ubuntu_mvp_debug_20261101.md) 仍保留；早期 FAIL 數字不是最新結果。
4. **最新完整後端 Feature 162 PASS／0 FAIL、691 assertions、46.70 秒**，證據 `mvp-feature-final-round11-20261102-024503.zip`。Round11 後 Unit、Schema、Concurrency、Flutter、真實外部服務、部署仍未在本輪驗證。
5. GitHub `DevApp_md` 只記錄文件；Ubuntu ZIP 工作樹不等於 GitHub 已同步程式。首次接手仍依本文件要求**先完整閱讀與回報，不自行修改**。
