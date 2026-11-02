# Shared Modules｜Booking 防超賣架構研究與實機修正（2026-11-02）

## 現象與原始證據
CONC-001：6 個相同教室重疊行程同時建立，原本 6 成功，預期 1；CONC-002：2 個重疊員工指派原本 2 成功，預期 1；CONC-003：6 人搶容量 1 的活動原本 6 confirmed，預期 1 confirmed + 5 waitlisted。測試：`tests/Concurrency/BatchGRaceTest.php`。

## 根因研究（有待獨立隔離級別／快照追蹤）
既有 `LockScope` 在交易內對 studio、staff、event 等固定資源列取得 `FOR UPDATE`；但 `ResourceConflicts` 與 `Capacity` 原本使用一般 SELECT、COUNT、MAX。MySQL InnoDB 在 REPEATABLE READ 可能沿用先前一致性讀取快照，導致取得鎖後仍看不到其他交易剛提交的資料。這是與實際修正效果吻合的技術解釋，**未以獨立追蹤證實所有路徑的根因**。

## 本機已驗證修正
Ubuntu 本機程式 `master` Commit **`264ff1f`**，訊息 `fix(concurrency): prevent resource conflicts and booking overselling`；未 Push。修改：`app/Services/Capacity.php` 5 處鎖定讀取，`app/Services/ResourceConflicts.php` 2 處鎖定讀取，`tests/Concurrency/BatchGRaceTest.php` 1 處將順序敏感斷言改為 canonicalizing，保留預期人數與資料庫數量驗證。3 files +8/-8；未修改 Migration、LockScope、TransactionRunner。

## 測試與風險
Concurrency 連續三輪各 10 PASS／31 assertions；Feature 165 PASS／701 assertions；Unit 43 PASS／99 assertions；Schema 91 PASS／120 assertions。合計 309 個不同測試案例、951 assertions、0 FAIL。工作目錄 `git status --short` 無輸出。

風險：`lockForUpdate()->pluck(...)` 可能增加鎖範圍與死鎖機率；後續大流量、補課、候補遞補、取消與不同索引路徑仍需驗證。不得為綠燈放寬名額或移除鎖。這是**本機並發修正驗收**，不等於正式部署、Flutter／Android／跨 App 整合驗收。

## 禁止重犯
不得再把已修復 CONC-001～003 當成 OPEN 或要求重新排查 Docker／Pest 專用帳號；如有新失敗先比對本機 SHA 與此證據。完整歷史及防重犯紀錄見 `docs/testing/KNOWN_ISSUES_AND_PREVENTION.md`。
