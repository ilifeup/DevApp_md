# Shared Modules V1｜2026-10-31 決議與 Claude 整批修正指令

> 基線：`Shared_Modules_V1_Batch4_Final_Handoff_20261031.zip`（本次只更新規格文件，不修改原始碼）。原始 HTML：r7.1；新增「10/31決議」頁籤為 r7.2。此決議高於原計畫書內互相衝突的 MEDIA-01 舊敘述；其他模組仍依原核准需求，不可擅自擴充。

## 一、正式決議（已確認）

1. **唯一音樂流程：先下載、下載完成後才播放。** 會員在 App 看見曲目，按「下載」；伺服器確認已登入、會員資格與該單曲權限後核發授權及加密檔下載；手機下載到私有暫存、完整驗證、原子切換至加密 Cache，才顯示「播放」。沒有完成的檔案不能播放。後續只從手機加密 Cache 播放；沒有第二套「線上即時串流」。
2. **離線播放：** 已完整下載、裝置與帳號相符、授權簽章有效且未過期，允許無網路播放；期限由後台設定，**最高 365 天**。重新連網時同步撤銷／權限／下架狀態。離線時無法即時獲知伺服器撤銷，應如實提示此限制；不另造背景 DRM。
3. **加密：保留既有整首 AES-256-GCM、X25519 金鑰包裝、Ed25519 授權簽章。** 先下載完才播放，已不需要 AES-CTR、分段 AES-GCM、HLS、Range、`StreamAudioSource` 邊播邊下載。音樂 Cache 僅保存密文，不保留永久明文檔；播放時記憶體短暫解密。目的為避免一般使用者直接取走音檔，不是正式 DRM。
4. **後台管理員單曲上傳上限 18 MiB = 18,874,368 bytes。** 選檔／提交時阻擋，Laravel 後端再次驗證；超限不得進入正式儲存、不得建立曲目。PHP/Web server request 限制要允許正常 18 MiB 上傳及 multipart overhead。不可採「先接受後刪除」。既有超額舊檔不得擅自刪除。
5. **刪除未要求的伺服器解密線上串流**及其短效播放 URL、串流路由、串流並發 4-slot Cache Lock、串流 503 重試、伺服器串流 Range 設計、Flutter 線上播放按鈕及 API、相應測試與文件。**保留**加密檔下載用的短效 `media.cipher` URL（用途不同）、離線授權、影片連結、正常本機播放 seek、會員權限。
6. **修正裝置換鑰：** 同一 `install_id` 不得靜默替換 `public_key`；公鑰變更要明確重新註冊／撤銷舊授權，必要時重新下載。先檢查現有 ID/公鑰指紋，無須預設新增大型 `kid` 歷史平台或 Ed25519 雙向 challenge。
7. **修正輪替與授權競爭：** 沿用既有 `rotation_id`、`rotation_mode`、`rotation_started_at`、`lockForUpdate()`；核發授權與輪替共用曲目鎖序，在交易內重讀狀態；輪替未完成不得核發新授權。避免重做第二套 ROTATING 系統。
8. **檔案一致性：** 沿用 FIX-01 的獨立密文檔名、完整下載驗證、授權／檔名原子切換，失敗保留舊檔與舊授權。清理工作不得刪除正在下載或仍被授權引用的檔案。
9. **不擴張功能：** 不做線上串流、即時播放、HLS、DRM、分段加密、播放人數統計、GA4/Mixpanel 心跳、額外 Cache Lock/Redis 系統或新模組。任何超出原始核准需求的功能先列差異表，不得自行實作。

## 二、已在 ZIP 找到的多餘串流實作：整批刪除／改寫

| 項目 | ZIP 中定位 | 處理 | 證據狀態 |
|---|---|---|---|
| 伺服器線上串流 URL | `backend/app/Http/Controllers/Api/MediaController.php`（`stream()`） | 刪除 | 待逐檔核對 |
| 伺服器解密音訊輸出 | `backend/app/Http/Controllers/Web/MediaFileController.php`（`stream()`） | 刪除；保留 cipher() | 待逐檔核對 |
| 線上播放 API 路由 | `backend/routes/api.php`（`/stream`） | 刪除相應 route | 已定位 |
| 線上串流 web 路由 | `backend/routes/web.php`（`media/stream`） | 刪除相應 route | 已定位 |
| Flutter 線上播放入口 | `mobile/lib/ui/screens/media_screens.dart`（`_playOnline`） | 刪除；只保留下載及本機播放 | 已定位 |
| Flutter 線上播放 URL 客戶端 | `mobile/lib/data/repository.dart`（`mediaStreamUrl`） | 刪除 | 已定位 |
| 伺服器串流並發設定 | `backend/config/media.php`（`max_concurrent_streams`） | 刪除 | 已定位 |
| 伺服器串流測試 | `backend/tests/Feature/MediaCenterTest.php`（`stream`） | 按引用實際搜尋、刪除或改寫 | 待逐檔核對 |
| Page Atlas 線上串流項目 | `docs`（`stream`） | 精確找出對應 registry 後刪除，勿刪其他功能 | 已定位 |

**注意：** `MediaFileController::cipher()` 是加密檔下載，`media.cipher` 短效簽名 URL 是保護下載，不是伺服器解密線上播放；不可誤刪。`memory_audio_source.dart` 的本機播放及進度控制不可因名稱含 source/stream 就刪掉。`MediaStorageService` 的解密可能仍供內容金鑰輪替或本機播放驗證使用，須做呼叫圖確認後才刪除無用分支。影片外部連結功能保留。

## 三、其他審查問題的最小解法（須逐一追蹤）

| 編號 | 問題 | 輕量修正／驗證 |
|---|---|---|
| A01 BOOK-01 | 候補入列過早檢查時段衝突 | 候補允許重疊；正式占位／轉正時重驗衝突、容量、欠費、資格；FIFO 不任意跳過 |
| A02 MEDIA-01 | 授權核發與內容金鑰輪替交錯 | 同曲目 DB 行鎖、交易內重讀狀態；輪替中拒發新授權 |
| A03 MEDIA-01 | 同裝置靜默更新 X25519 公鑰 | 禁止直接覆蓋，顯式重綁定及舊授權處理 |
| A04 MEDIA-01 | 五分鐘線上播放 URL 可轉傳 | **整項刪除**，不是另加防護；加密檔下載 URL 保留 |
| A05 MEDIA-01 | 串流並發 Cache Lock 釋放時機 | **整項刪除**，不再有線上串流工作數需求 |
| A06 MEDIA-01 | 整檔記憶體峰值 | 18 MiB 上傳硬限制；下載採寫 `.part`，驗證與播放時量測記憶體；不重做分段加密 |
| A07 MEDIA-01 | 下載與孤兒檔清理競爭 | 不掃正在下載檔；刪除前再次核對授權引用；補並發測試 |
| A08 MEDIA-01 | 重新下載授權／檔案切換失敗 | 保留 FIX-01 原子切換；失敗不破壞舊可用版本 |
| A09 MEDIA-01 | 金鑰輪替與上傳競爭 | 共用曲目行鎖與一致鎖序；保留既有 rotation state |
| A10 MEDIA-01 | 授權查詢可能造成永久撤銷 | 查詢與永久撤銷分離；核對原業務規則，補測試 |
| A11 PAYGATE-01 | Webhook ID 截斷可能碰撞 | 使用完整 event ID 雜湊做唯一鍵，維持同交易去重 |
| A12 PAYGATE-01 | 交易外讀取資料過期 | 交易內鎖定後重驗 provider、金額、幣別與狀態 |
| A13 PA-01 | Cache Lock TTL 與同步並發 | 優先沿用交易內鎖與重讀，雙程序實測，不重建鎖服務 |
| A14 MEDIA-01 | 伺服器 Range／MIME | **伺服器 Range 刪除**；本機播放器正確 MIME 與 seek 保留並測試 |
| A15 UI | null 容量被顯示成 0 | 顯示「尚未設定容量」，不修改容量規則 |

> 上表是先前靜態審查與本次決議的追蹤清單；不是聲稱每項已在真實資料庫／Android 上重現。修正前要先由 Claude 在本批 ZIP 中定位原始碼與呼叫鏈，對不存在的問題註明「無此問題／附證據」，不可為了湊清單新增程式。

## 四、Claude 必須執行的流程與交付

1. **先盤點再改碼：** 以本 ZIP 為唯一基線，列出所有 API route、Controller、Service、Filament action、Flutter 畫面與按鈕，對照 r7.2 決議和原計畫書；分類「已核准／必要技術支援／未授權新增／待確認」。本次先清理已確認的線上音訊串流，其他 44 模組不得憑猜測刪除。
2. **列出依賴／刪除清單：** 刪除 route、方法、imports、設定、環境變數說明、測試、Page Atlas registry、文件與前端按鈕；查找全專案 `stream`, `media.stream`, `mediaStreamUrl`, `max_concurrent_streams`, `Retry-After` 等引用。只刪與**線上音訊串流**有關者，避免誤刪一般 HTTP streaming、影片、檔案傳輸及本機播放器。
3. **唯一 UI 狀態：** 未下載／下載中／已下載可播放／授權過期或撤銷／下載失敗。未下載只有「下載」，下載成功才顯示「播放」；必要時提供「重新下載」及「移除快取」。不新增線上播放按鈕。
4. **18 MiB 硬上限：** 更新 `config/media.php`、Filament `maxSize`、Service byte validation、API 顯示與錯誤文案；測 18,874,368 bytes 接受、18,874,369 bytes 拒絕；不得在超限後建立正式檔案／DB 紀錄。
5. **修正授權及金鑰競爭：** 實際檢查 `MediaDevices`, `OfflineLicenses`, `RotateContentKey`, `MediaStorageService`；共用鎖序；補換鑰、輪替中發授權、失敗重試及兩程序競爭測試。
6. **下載／離線測試：** 已授權下載成功、無權限拒絕、斷網中斷、錯誤長度、檔案損毀、裝置換帳號、撤銷後重新連線、離線授權 365 天邊界、手機時鐘回撥、重新下載寫入失敗保留舊檔、孤兒檔清理競爭。
7. **嚴禁假驗收：** PHP -l／Pint／Dart 括號檢查只能算靜態檢查；Migration、Pest、Flutter analyze/test/build、Android 實機、雙程序並發未跑一律標 `NOT RUN` 或 `BLOCKED`。PAYGATE FakeProvider 不可宣稱正式可收款；CAL-02 無憑證不得宣稱完成整合；SEARCH-01 仍需原規格。
8. **一次整合交付：** 不動正式資料庫、不部署、不自行 Git commit／PR／merge；集中修改，最後提供單一完整 ZIP、檔案差異清單、測試結果、未完成清單，並同步更新完整 HTML 計畫書、Page Atlas 與 `docs/PROGRESS.md`。所有未經測試的功能標 `IMPLEMENTED / UNVERIFIED`。

## 五、Claude 可直接執行的指令

請以 `Shared_Modules_V1_Batch4_Final_Handoff_20261031.zip` 為唯一程式基線，嚴格依本《10/31決議》一次整合修正。先完成全模組需求／程式差異盤點，將所有未授權新增功能列出證據；已確認多做的 MEDIA-01 伺服器解密線上串流、5 分鐘播放 URL、對應 routes、Flutter 線上播放、串流 Cache Lock／4 並發限制、503 重試與 Range 規格必須完整刪除，**但不可刪加密檔下載、短效 cipher URL、離線授權、影片連結及本機播放 seek**。音樂唯一流程為會員有單曲權限 → 點下載 → 完整下載、驗證並寫入手機加密快取 → 才能播放；後台授權最長 365 天。保留 AES-256-GCM／X25519／Ed25519，管理員單曲上傳嚴格上限 18 MiB、超限立即拒絕。同步修正 A01～A15 中仍存在的問題，沿用既有 DB 鎖與輪替狀態，不新增大型系統。所有變更補測試；無法執行的測試明確標 BLOCKED。不得擅自提交 Git、部署或操作正式 DB。最後交付單一完整 ZIP、r7.2 後續版 HTML、進度與差異報告，供 ChatGPT 再審。