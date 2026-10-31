# DevApp_md｜Shared Modules V1 AI 計畫書管理

> A 階段：ChatGPT Plus／Claude Pro → GitHub Markdown。修改前請讀 [AGENTS.md](AGENTS.md)。

## 最新 P0 追加與 UI 規則

- [TIPS-01 共用按鈕提示模組](modules/TIPS-01.md)：三 App、後台設定提示文字與秒數、Flutter 輕量提示元件。
- [Icon 優先、文字其次、按鈕精簡；P0 TIPS-01 正式決議](ChangeLog/2026-11-01_SharedModulesV1_P0_TIPS01_影音Icon優先與提示模組.md)：最新規則，覆蓋過去絕對 Icon-only 的說法。

## 2026-11-01 最新正式決議

- [影音 Icon-only 最高原則與批次 F 全 10 模組核准](ChangeLog/2026-11-01_SharedModulesV1_批次F十模組核准與影音Icon最高原則.md)：適用 3 App，FAMILY 優先、SHOP 三 App 共用 WooCommerce、NOTIFY 沿用現有通知流程，簡單設計、不過度開發。

## 最新執行決議（歷史交付指令）

- [2026-11-01｜批次 1～2 修正封存交付指令](ChangeLog/2026-11-01_SharedModulesV1_Claude_批次1至2修正封存交付指令.md)：**本次只交付已修正成果；不啟動批次 F／G。**

## 下一批次開發指令

- [2026-11-01 Claude 下一批次整合開發指令](ChangeLog/2026-11-01_SharedModulesV1_Claude_下一批次整合開發指令.md)：先清除錯誤設計、逐項核對 A01–A15，再一次完成下一核准批次。**指令存於 ChangeLog；審查結論另記入正式模組與追蹤檔。**

## 專案入口

- [PROJECT.md](PROJECT.md)：專案總覽、45 模組索引、進度與待核對差異。
- [modules/](modules/)：從 r7.2 HTML 拆分的 **45 張模組卡**；均標示待核對，不能當作已驗收。
- [changes/CHG-20261031-MEDIA-01.md](changes/CHG-20261031-MEDIA-01.md)：10/31 決議與 Claude 整批修正指令。
- [tracking/MEDIA-01.md](tracking/MEDIA-01.md)：需求、程式、測試三版本獨立追蹤。
- [原始完整 HTML r7.2](docs/reference/Shared_Modules_V1_專案開發計畫書_r7.2_1031決議.html)：完整保留 14 Tab、45 卡、核心流程、DB、規則與 Page Atlas。
- [Claude 指令原文](docs/reference/Shared_Modules_V1_1031決議_Claude整批修正指令_原文.md)：未改寫的原始來源。
- [DevApp_md v1.0 參考計畫](docs/reference/DevApp_md_v1.0.md)：文件管理架構規劃。

## 更新原則

- 一個 AGENTS.md 管理所有 AI；一模組一份正式規格；新來源先比對，不擅自變更業務規則。
- 三版本（需求／實作／驗收）分離；未經驗證不得標示完成。
- 本次屬**來源匯入與文件拆分**，不是程式修正或正式驗收。
- 原始 HTML 中的跨模組章節仍以原始檔保留；如拆分卡片有格式損失，回查原文。
