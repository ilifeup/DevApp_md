# Ubuntu Shared Modules MVP｜B0 本機 Git 基線與安全處理（2026-11-02）

> 狀態：**B0 已完成（本機）**。本文件是執行紀錄，不是新功能規格；DevApp_md 只存文件，不存 Ubuntu MVP 原始碼或密碼。

## 可核對的本機 Git 證據
- 工作目錄：`/home/media/GitDev/ShareModules-MVP-Test/Shared_Modules_V1`。
- Git 原本未初始化；現在已有**本機 root commit**：`e20b294`（短 SHA），訊息 `baseline: round11 local MVP snapshot`。
- 分支：`master`；標籤：`round11-baseline`。
- Commit 輸出：**561 files changed, 59352 insertions(+)**。
- `git status --short` 空白（工作目錄乾淨）；`git remote -v` 空白（未設定 GitHub 遠端）；沒有推送程式。
- 上述結果來自使用者 2026-11-02 提供的終端輸出；**不是本文件更新者遠端登入 Ubuntu 重新驗證**。

## B0 安全範圍
- `backend/.env.testing`、`backend/.mvp-db-password`：保持在本機，不追蹤；密碼檔權限與審查帳號 ACL 已做隔離。
- `backend/.env.testing.example` 中非空 `APP_KEY` 已清空；環境範本可追蹤，實際秘密不可追蹤。
- `tools/g_acceptance/docker-compose.yml` 第 14、16、41 行密碼欄位經遮罩檢查屬 `HARDCODED_OR_OTHER`；**未曝光值，也未證實是否正式密碼**。為安全起見加入根目錄 `.gitignore`（`/tools/g_acceptance/docker-compose.yml`），並用 `git rm --cached` 從索引排除，原檔留在硬碟。排除後暫存數 562→561。
- 已忽略備份檔、產物、敏感檔；禁用詞已從本機 README 的一處替換為中性稱呼。遵守 [AGENTS.md](../../AGENTS.md) 的保密規則。
- 先前靜態憑證掃描找到 17 個疑似位置，多數是程式結構或環境變數引用；**規則式掃描不等於全面安全稽核**，本機基線不得直接視為可公開程式碼。
- 曾有 `git diff --cached --check` 提示兩處空白格式問題，未為 B0 改動來源，不影響 root commit 建立；如要整理，留待後續集中處理。

## 不可誤判
- **B0 Git 完成 ≠ B1 修正完成 ≠ 47 模組全部驗收完成**。
- Round11 Feature 162 PASS／0 FAIL、691 assertions 是較早實測；Unit／Schema 在 Round11 後尚未完整重跑，Concurrency／Flutter／Android／E2E 尚無本輪完成證據。
- 本機 Git 只有標籤，沒有名為 `round11-baseline` 的分支；當前分支是 `master`。
- 不得把此本機 commit SHA 當作 GitHub DevApp_md 或 Shared Modules 雲端 repo 的 commit SHA。
- 本機 Docker Compose 被排除追蹤；未來如要可重建驗收環境，應在**不含密碼**的前提下另評估 example 設定檔，不要直接提交現有檔案。

## 下一步
B1 先讀 [B1 審查與待核准事項](UBUNTU_B1_REVIEW_20261102.md)，確認範圍後才集中修改。禁止自動部署、推送程式、變更正式資料庫或 iStudy。
