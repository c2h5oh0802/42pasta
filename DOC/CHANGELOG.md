# 開發更新紀錄 (Changelog)

## [2026-05-11] MVP 核心架構建立與清理流程優化

### 新增 (Added)
- 初始化 `index.html`，建立基於 MediaPipe Face & Hand Landmarker 的多模態輸入環境。
- 引入 `Matter.js` 建立 2D 物理世界。
- 實作「第一人稱視角」的虛擬手臂與骨架發光特效。
- 實作 `CLEANING` 階段的子狀態機：`INIT` -> `CLEAR_TRASH` -> `TRANSITION` -> `WIPE_TABLE`。
- 新增模擬訂單系統，根據餐點隨機生成不同形狀的實體垃圾 (盤子、刀叉、杯子、紙團)。
- 新增 `WASHING` 洗碗階段的來回手搓位移累積判定。
- 新增 `ORDERING` 階段基於微笑辨識的滿意度增減系統。

### 變更 (Changed)
- 將垃圾判定方式由「推進特定感應區」改為「推落桌子邊界外」。
- 調整雙手碰撞體大小 (半徑加大為 60)，並綁定於手掌心，提升撥垃圾的手感。
- 將擦桌子機制獨立為 `WIPE_TABLE` 階段，雙手此時會停用物理推動，專注於座標重疊擦拭。

### 安全與基礎建設 (Infrastructure)
- 初始化 Git 儲存庫。
- 建立 `DOC/AGENTS.md` 專案狀態文檔與此 `CHANGELOG.md` 更新紀錄檔。