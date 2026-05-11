# 開發更新紀錄 (Changelog)

## [2026-05-11] Phase 4: 廚房料理系統 (Cooking Mini-Game)

### 新增 (Added)
- **接單流程重構**：玩家在櫃檯的點餐氣泡上停留 1 秒後不再「直接出餐」，而是將客人的狀態切換為 `WAITING_FOOD`，並產生一筆 `activeOrder` 傳送到廚房。
- **廚房料理狀態機 (`cookState`)**：實作了 6 階段的煮麵流程：
  1. `PUT_PASTA`: 從感應區抓起「混泥土色」的生麵放入鍋中。
  2. `BOILING`: 義大利麵在鍋中煮沸的進度條。
  3. `PLATE_PASTA`: 從鍋中撈出熟麵放到盤子上。
  4. `ADD_CILANTRO`: 判斷 `activeOrder` 是否需要加香菜，若是則需抓取香菜放上盤子，否則自動跳過。
  5. `STIRRING`: 在盤子上方進行來回/畫圈運動累積攪拌進度。
  6. `SERVE`: 將完成的餐點抓取至紅色的「出餐口」感應區。
- **抓取系統 (Grab Mechanics)**：在料理階段，當手部位於特定感應區時，會自動改變 `dragItem`，並且手部會畫出相對應的食材圓圈（例如抓起香菜時手部會顯示綠色圓圈）。
- **訂單文字提示**：廚房左上角會明確顯示目前的訂單是「🍝 混泥土義大利麵 (加香菜)」或「(不加香菜)」，以及當前步驟的操作指引。
- 完成出餐口操作後，會根據客人在櫃檯剩餘的耐心值，直接加分或是將物理金幣丟在櫃檯的桌面上。

### 變更 (Changed)
- 在廚房中，優先判斷是否有 `activeOrder` 存在。如果沒有正在處理的訂單，廚房才會顯示原本的「洗盤子 (`WASHING`)」小遊戲。

---

## [2026-05-11] Phase 3: CustomerManager 客人排隊與點餐系統

### 新增 (Added)
- 實作客人物件與陣列 (`customers`)，在進入遊戲後會定時生成客人 (根據關卡的上限)。
- 限制「排隊人數需小於關卡總座位數 (`LEVELS[i].tables`)」才會產生新客人。
- 若客人因等待過久氣走，會導致 `negativeReviews` (負評) +1，顯示於左上角 HUD，且每多一個負評，新客人出現的間隔就會多加 1.5 秒。
- 實作「客流排隊顯示」，在 `COUNTER` 畫面中會依序畫出後方的排隊人潮。
- 實作 **物理金幣系統 (`droppedMoney`)**：
  - 出餐時，若客人耐心低於一半，會將錢丟在桌上，產生一個 Matter.js 的物理金幣。玩家必須伸手觸碰 (Hover/Grab) 金幣才能獲得該筆分數。

### 變更 (Changed)
- 移除全域舊版 `satisfaction` 變數與邏輯，全面改由陣列首位客人的 `patience` 接管 HUD UI (`#satisfaction-bar`)。
- 更新 `setMicroLocation` 的物理世界清理邏輯：
  - 離開 `COUNTER` 時，桌上的錢會從 `world` 中移除 (但保留在陣列)。
  - 回到 `COUNTER` 時，桌上的錢會重新加入 `world`。

---

## [2026-05-11] Phase 2: 全域手勢滯留導航與 Bug 修正

### 新增 (Added)
- 實作「主選單/關卡選單/結算畫面」的 UI 手勢滯留操作 (`checkUiDwellActions`)。
- 選單上的卡片與按鈕在手部滯留時，會動態長出黃色/綠色的環形進度條視覺反饋。
- 新增全域錯誤捕捉機制 (`window.addEventListener('error')`)，防止畫面無聲凍結，方便除錯。
- 手部停留空間導航箭頭 1.5 秒自動觸發空間切換，並附帶冷卻時間。

### 變更 (Changed)
- 將選單覆蓋層 (`.panel-overlay`) 設為半透明，並修改主迴圈確保「全時渲染攝影機畫面與第一人稱虛擬手部」，解決在非遊戲狀態下看不到手的問題。
- 優化 `updateUiTargets` 的綁定邏輯，針對 `div` 元素優先執行其綁定的 `onclick` 函數，相容各種瀏覽器模擬點擊行為。
- `handleHandLogic()` 改為全域持續執行，不再僅限於 `GAMEPLAY` 狀態。

### 修復 (Fixed)
- **重大崩潰修復**：修正 `trashBodies` 在頂層未給予初始值陣列導致的 `ReferenceError`。
- **重大崩潰修復**：修正 UI 手勢滯留進度達 100% 觸發狀態切換後，導致的 `Cannot read properties of null` 錯誤。現在會在觸發行為後使用 `return` 提早跳出防呆。

---

## [2026-05-11] Phase 1: 宏觀 UI 與存檔系統建立

### 新增 (Added)
- 新增 `assets/img/ui` 與 `assets/img/bg` 資料夾，為後續圖片置換做準備。
- 實作遊戲巨觀狀態機 (Macro States)，包含：標題 (`TITLE`)、關卡選擇 (`LEVEL_SELECT`)、遊戲中 (`GAMEPLAY`)、結算 (`LEVEL_RESULT`)。
- 新增限時挑戰系統，建立三個初始關卡 (Level 1~3)，包含目標金額、倒數時間與解鎖條件。
- 實作基於 `localStorage` 的存檔機制，記錄過關星星數以解鎖後續關卡。
- 建立微觀空間系統 (Micro Locations)，將遊戲邏輯劃分為 `COUNTER` (櫃檯)、`KITCHEN` (廚房) 與 `DINING_ROOM` (用餐區)。

### 變更 (Changed)
- 將 `WASHING` 邏輯綁定至 `KITCHEN` 空間，`CLEANING` 邏輯綁定至 `DINING_ROOM` 空間。

---

## [2026-05-11] MVP 核心架構建立與清理流程優化

### 新增 (Added)
- 初始化 `index.html`，建立基於 MediaPipe Face & Hand Landmarker 的多模態輸入環境。
- 引入 `Matter.js` 建立 2D 物理世界。
- 實作「第一人稱視角」的虛擬手臂與骨架發光特效。
- 實作 `CLEANING` 階段的子狀態機：`INIT` -> `CLEAR_TRASH` -> `TRANSITION` -> `WIPE_TABLE`。
- 新增 `WASHING` 洗碗階段的來回手搓位移累積判定。
- 新增 `ORDERING` 階段基於微笑辨識的滿意度增減系統。