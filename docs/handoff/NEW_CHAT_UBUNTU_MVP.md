# 新對話接手｜Ubuntu Shared Modules V1 MVP

直接接手 Ubuntu 本機 Shared Modules V1 MVP 的 Laravel/Pest Debug。**先讀**：

1. [共用 AI 規則](../../AGENTS.md) 與 [專案總覽](../../PROJECT.md)。
2. [Ubuntu 固定環境](../environment/ubuntu_shared_modules_mvp.md)。
3. [Pest DB 安全](../testing/pest_database_safety.md)。
4. [最新 Debug 狀態及 11 Fail](ubuntu_mvp_debug_20261101.md)。
5. 相關 [modules/](../../modules/) 與 [ChangeLog/](../../ChangeLog/) 最新正式決議，先核對，不以舊 r7.2 覆蓋。

本機 backend：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1/backend`；PHP 容器 `eachcore-dev-php-1`。Pest 專用 DB **`shared_pest_test`**；**`shared_mvp_test` 不可跑 Pest**。保留 TestDatabaseGuard、iStudy 完全不動。

最新實測：Unit **43/43 PASS**、Schema **91/91 PASS**、BatchF **20 PASS / 11 FAIL**；完整 Feature 在最新改碼後 **NOT RUN**；Concurrency **NOT RUN**。

下一步：以本機最新 MVP 源碼為準，集中調查 BatchF 11 個失敗，不要重複詢問已寫入文件的固定環境；然後安全地重跑 Unit → Schema → Feature，確認 Guard 才跑 Concurrency。修改前備份；不得為測試綠燈放寬業務、安全與資料庫規則。**DevApp_md 是文件庫，不是程式碼庫。**
