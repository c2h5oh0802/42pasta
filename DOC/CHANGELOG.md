# 開發更新紀錄 (Changelog)

## [2026-05-11] Phase 2: 手部滯留導航 (Dwell Navigation)

### 新增 (Added)
- 實作導航區域偵測 (`NAV_ZONES`)，定義三個空間的邊緣觸發區。
- 新增 `checkDwellNavigation()` 函數，追蹤手部在導航區域的停留時間。
- 手部停留 1.5 秒自動觸發空間切換，切換後有 1 秒冷卻時間。
- 導航箭頭新增滯留視覺反饋：脈衝發光、環形進度條、百分比文字。
- 進度條顏色隨進度變化 (黃色 → 70% 後轉綠色)。

### 變更 (Changed)
- 重構 `drawNavigationArrows()`，改用資料驅動迴圈繪製。
- `handleHandLogic()` 中新增 `activeHandPositions` 收集，供導航偵測使用。

---

## [2026-05-11] Phase 1: 宏觀 UI 與存檔系統建立

### 新增 (Added)
- 新增 `assets/img/ui` 與 `assets/img/bg` 資料夾，為後續圖片置換做準備。
- 實作遊戲巨觀狀態機 (Macro States)，包含：標題 (`TITLE`)、關卡選擇 (`LEVEL_SELECT`)、遊戲中 (`GAMEPLAY`)、結算 (`LEVEL_RESULT`)。
- 新增限時挑戰系統，建立三個初始關卡 (Level 1~3)，包含目標金額、倒數時間與解鎖條件。
- 新增 HTML/CSS 覆蓋層 (`panel-overlay`) 處理所有選單 UI。
- 實作基於 `localStorage` 的存檔機制，記錄過關星星數以解鎖後續關卡。
- 建立微觀空間系統 (Micro Locations)，將遊戲邏輯劃分為 `COUNTER` (櫃檯)、`KITCHEN` (廚房) 與 `DINING_ROOM` (用餐區)。
- 在畫面上繪製「空間切換提示箭頭」，準備供手勢操作使用。

### 變更 (Changed)
- 將原先的 `setGameState` 拆分為巨觀 (`changeMacroState`) 與微觀 (`setMicroLocation`) 控制。
- 將 `WASHING` 邏輯綁定至 `KITCHEN` 空間。
- 將 `CLEANING` 邏輯綁定至 `DINING_ROOM` 空間。
- 將微笑滿意度邏輯綁定至 `COUNTER` 空間。
- 更新 `DOC/AGENTS.md` 反映最新系統架構與 Phase 1 成果。

---

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