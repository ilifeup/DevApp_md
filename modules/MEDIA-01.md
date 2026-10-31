---
module_id: MEDIA-01
spec_version: r7.2-import
spec_status: source_imported_pending_review
source: docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html
updated_at: 2026-11-01
---

# MEDIA-01｜r7.2 原始模組卡拆分

> **來源保真聲明：** 本檔從 r7.2 HTML 的對應模組卡轉出，尚未逐項核對正式核准來源；不得把匯入狀態視為程式已完成或驗收通過。HTML 原始版保留於 [參考檔](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)。 **10/31 正式決議優先於舊的串流敘述，詳見下方連結。**

MEDIA-01影音中心 S10P0○未開始
r7.1 實作（未驗收、非正式 DRM）
- 加密協定 SMv1-x25519（D15 有條件核准）：X25519＋HKDF-SHA256（salt＝eph.pub‖device.pub、info "SMv1-cek-wrap"）＋AES-256-GCM（AAD＝license_id）；內容 "SMv1"‖iv‖tag‖ct；授權 Ed25519 簽 license_json 原始位元組；PHP 獨立腳本 12 項斷言 PASS，Dart 交叉測試 NOT RUN。
- Flutter：just_audio 線上播放（5 分鐘簽名串流）、逐曲離線下載、播放前驗章／綁定／連線檢查（只有 ok 可播）／時間回撥／到期拒播、記憶體解密；FIX-01 重新下載版本化切換；換帳號與解除裝置清空。
- 後端：四道關卡、D8 裝置名額（以裝置計）、重新驗證、遠端解除、T11 主金鑰輪替可續跑、T12 內容金鑰輪替（FIX-02 可續跑、寬限起算持久化）、T14 調降不撤銷；串流並發上限；孤兒檔清理。
- 限制：私鑰軟體保存（非硬體不可匯出）；完全離線無法即時撤銷；單曲 50 MB 整檔加密，分段為必要後續改善；Android 實機未測。

功能
- 入口三態：hidden、verification_gate（訪客可見但鎖住）、public；進入目錄需 verified_status=2
- 音樂逐曲、YouTube 逐片都要授權（ACCESS-01 三層＋第 4 道 entitlement 檢查）
- 內含 P0 儲存能力：MediaStorageService＋media_files（私有 disk、sha256、加密旗標）；上傳、加密、私有儲存都在本模組，不依賴 FILE-01
- D8 裝置名額：預設每位會員 1 台有效離線授權裝置，後台可改為 2、3 台等；名額以 media_devices（會員＋裝置）計算，不以 offline_licenses 筆數計算，同一台裝置授權多首歌只占 1 個名額
- 名額範圍：只限制離線音樂；不限制 App 登入數、線上播放與其他模組；超過名額時回 MEDIA_DEVICE_LIMIT，App 仍可正常登入與線上使用
- 裝置身分：App 安裝識別（install_id，安裝時產生的 UUID）＋裝置金鑰對；私鑰優先放 Android Keystore、iOS Keychain／Secure Enclave，不能用時以 secure storage 保存並標記 key_storage=software；不使用 IMEI、MAC、硬體序號
- 換機：會員在新手機的「我的離線授權裝置」頁遠端解除舊手機，舊手機不必在線；伺服器立即撤銷該裝置與其全部授權；舊手機下次連線時失效並清除本機密文與金鑰；舊手機完全離線時無法立即失效
- 防濫用：media_device_events 記錄新增、解除、重新授權、名額拒絕；不採固定 30 天禁換；在設定期間內切換次數超過門檻時回 REVERIFY_REQUIRED，要求重新驗證身分；管理員可在後台處理例外
- 身分再驗證（可替換）：IdentityReverifier 介面，方法依 App 設定啟用：重新輸入密碼（預設，不需外部服務）、Email 驗證碼、SMS（只在已選定並設定供應商時）；不得強制依賴尚未確定的 SMS 供應商；驗證結果短效（例如 15 分鐘）只用於本次換機
- 金鑰：每曲一把內容金鑰 CEK（AES-256-GCM），存在 media_content_keys（版本化），以主金鑰包裝；主金鑰本體在環境或 KMS，資料庫只存參照；核發時以裝置公鑰重新包裝；本機只存密文與受保護的包裝金鑰，不產生永久明文
- T11 主金鑰輪替：建立新主金鑰（active）→ 背景逐批把 CEK 重新包裝（舊主金鑰 retiring 期間仍可解包）→ 全部完成且驗證後才把舊主金鑰標 retired；中途失敗可續跑、不刪舊版，既有離線授權與密文都不受影響
- T12 內容金鑰輪替：正常輪替＝產生新版本密文與 CEK，新授權用新版本，舊版本進入 grace 期（後台設定），期間既有授權照常、到期後重連檢查回 rekey_required；疑似外洩＝舊版本立即標 compromised，停止核發舊版授權，重連檢查回 rekey_required，完全離線的裝置仍無法保證即時失效
- T14 調降上限：上限由 2 改為 1 時不自動撤銷既有裝置，只阻止新增；後台顯示超額；管理員可指定撤銷某台裝置，必填原因並寫入稽核
- 離線授權：含 license_id、會員、歌曲、裝置、伺服器簽發時間、到期時間、license_version、key_version，並由伺服器簽章（App 以內建公鑰驗證）；預設最長 365 天（後台可調），不代表期間內的撤權都能立即生效
- 時間回撥偵測只是風險控制：裝置時間早於最後已知伺服器時間時拒絕離線播放，不宣稱能完全防止改時間
- 重新連線：下一次播放前呼叫 /av/licenses/check，重新驗證會員資格、內容權限、下架、裝置與授權撤銷、key_version
- 限制聲明：加密與私有儲存只降低直接複製，無法完全防止錄音、系統破解或已取得內容的擷取；YouTube 只嵌入官方播放器；UI 只有「播放」

核心流程入口 → 驗證 → Page、List ACL → 目錄 → Item ACL＋entitlement → 註冊裝置（交易內檢查名額）→ 核發逐曲離線授權（簽章＋裝置包裝 CEK）→ 離線播放 → 重連檢查
資料表media_files（本模組 P0 建立）、media_collections、media_tracks（+current_key_version）、media_content_keys、media_master_keys、media_videos、media_entitlements、media_devices、offline_licenses、media_device_events、identity_reverifications；settings 的 media.offline.*
APIGET /av/collections、/av/tracks、/av/videos；POST /av/devices/register；GET /av/devices；DELETE /av/devices/{id}；POST /av/tracks/{id}/license；POST /av/licenses/check；POST /me/reverify（方法依 App 設定）
Filament 後台影音目錄、上傳與加密、授權規則；離線設定（裝置上限、授權天數、切換門檻、grace 期）；會員裝置與事件查詢、超額標示、管理員指定撤銷（必填原因）；主金鑰輪替進度與續跑；內容金鑰正常輪替或標記外洩
Flutter App影音中心、播放器、離線授權狀態、「我的離線授權裝置」頁（裝置名稱、最近授權時間、狀態、解除）
規則、去重已驗證會員不等於擁有所有曲目；deny 優先；名額以裝置計，不以歌曲數計
舊編號S10 分支

## 來源與衝突處理

- [原始 r7.2 HTML](../docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)
- [10/31 決議與 Claude 整批修正指令](../changes/CHG-20261031-MEDIA-01.md)
- 匯入內容可能包含舊版技術方案；如與 10/31 決議或後續正式核准規格衝突，須先標示衝突，不得直接執行。
