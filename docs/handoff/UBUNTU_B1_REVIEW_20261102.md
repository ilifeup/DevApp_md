# Ubuntu Shared Modules MVP｜B1 Claude Code 審查與待核准事項（2026-11-02）

> 狀態：**僅審查，待使用者核准；尚未證實已修改程式**。來源為使用者提供的 Claude Code 終端截圖（部分畫面），不是完整審查報告；不可把截圖看不到的內容補成已完成事實。

## 已知建議與風險
- 先重跑失敗測試，優先處理 **F1、F3、F5**，再集中修正。
- F1：撤銷後 check 回 `ok`；裝置解除／換鑰後回 `revoked`；金鑰外洩後回 `rekey_required`。**撤銷類測試必須先通過**，不得為綠燈放寬授權語意。
- F3：寄信失敗時仍回 200；截圖顯示目前完全沒有測試呼叫 `password/forgot` 路由。需確認寄信同步／非同步語意，完整解法未在 B1 範圍。
- F5：連續簽到、簽退、重複掃描；截圖標示為 P1，是否納入 B1 **尚待核准**。
- Claude 提議 B1 結束時統一回歸 `Unit`、`Schema`、`Feature`、`Concurrency`；修復後集中測試，不為每個小問題零碎提交。
- 執行方式建議：Claude 提供 patch 與報告，`media` 帳號在明確授權後套用；只使用既有測試容器，不重啟、不改 Compose。測試密碼透過不顯示值的安全輸入方式提供；不得寫入命令列或報告。
- 風險：`shared_pest_runner` 建 Trigger 可能失敗（BUG-003），應停止回報而非調整 MySQL 全域設定；缺 `pcntl/posix` 導致 Concurrency skipped 時不能標為 PASS；Concurrency 可能暴露實際缺陷；測試共用容器增加 CPU/DB 負載。
- F3 寄信同步／非同步回應時間仍可能不同，截圖標示完整解法不在 B1。
- 截圖提到 `PROGRESS.md` 內 F1 舊修法與工期尚待更新；正式文件要在核准後以實測結果更新，不能先寫「已修復」。

## 使用者待核准的六項
1. F5 是否納入 B1（Claude 建議納入；目前**未核准**）。
2. F1 是否需要更嚴格的撤銷檢查與語意（目前**未核准**）。
3. 五組並發測試是否全部執行；至少需先核對 DB Guard、測試環境與可用擴充套件。
4. 採用 patch 加腳本，由 `media` 帳號執行（目前**未核准**）。
5. 測試 DB：允許對 **`shared_pest_test`** 執行明確列出的清空／重建測試（目前**未核准**；不得碰其他 DB）。
6. README 禁用詞是否於 B1 前修正並單獨提交；**B0 本機基線已處理一處**，應先比對現況，不可重做或建立多餘 commit。

## 新增固定交付規則：Claude Code 完整 MD 報告
- **每次審查、測試、修復、階段交接，必須輸出完整 UTF-8 `.md` 報告**，不能只顯示在終端機或要求使用者連續截圖。
- 報告需列：日期與時區、工作樹/commit、範圍、證據、問題 ID、確定根因/待驗證、變更檔案、DB 影響、執行命令（秘密遮蔽）、測試 PASS/FAIL/SKIP、風險、待核准事項、下一步。
- 優先在 Claude 有權寫入的報告目錄 `/home/claude_audit/reports/shared-modules-mvp/` 生成；再**確認 media 帳號可讀取**，提供實際絕對路徑與檔名。無權寫入時輸出 Markdown 到可讀檔案或提供安全交付方式，**不得虛稱已建立**。
- 若需要從使用者電腦直接下載，可由 `media` 在 RustDesk 中使用檔案管理員複製；ChatGPT 不能僅憑 Claude 終端路徑捏造 sandbox 下載連結。
- 正式需求變更須另行核准；報告和 GitHub 文件同步不得被當作自動核准。

## 對接文件
- [B0 本機 Git 基線](UBUNTU_LOCAL_GIT_BASELINE_B0_20261102.md)
- [Ubuntu Debug 交接](ubuntu_mvp_debug_20261101.md)
- [已知錯誤防重犯](../testing/KNOWN_ISSUES_AND_PREVENTION.md)
- [AI 共用規則](../../AGENTS.md)
