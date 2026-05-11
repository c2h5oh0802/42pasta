# AI Agent 開發狀態 (Project Context)

這是一款使用 HTML5 Canvas 與 MediaPipe Hands/Face 製作的第一人稱視角互動網頁遊戲，名為「42號義麵店」。

## 目前系統狀態 (Phase 1)
- **零建置環境 (Zero-build setup)**：純 HTML/CSS/JS 實作，單一檔案 `index.html`。
- **UI 與存檔系統**：
  - 實作了遊戲巨觀狀態機 (Macro States)：`TITLE` -> `LEVEL_SELECT` -> `GAMEPLAY` -> `LEVEL_RESULT`。
  - 使用 `localStorage` 實作關卡解鎖與星星數存檔 (`42PastaSave`)。
  - 設計三關限時挑戰，可於上方 HUD 檢視目標金額與倒數時間。
- **空間狀態與導航 (Micro Locations)**：
  - 玩家可在 `COUNTER` (櫃檯)、`KITCHEN` (廚房) 與 `DINING_ROOM` (用餐區) 三個空間中切換。
  - (MVP) 畫面兩側與上方已繪製切換提示箭頭，準備供後續手勢操作串接。
- **物理與洗碗小遊戲整合**：
  - 洗碗 (`WASHING`) 邏輯已搬移至 `KITCHEN`。
  - 擦桌與撥垃圾 (`CLEANING`) 邏輯已搬移至 `DINING_ROOM`。
- **手部滯留導航 (Dwell Navigation)**：
  - 將手移至畫面邊緣的導航箭頭區域並停留 1.5 秒，即可切換空間。
  - 環形進度指示器 + 脈衝發光視覺反饋。
  - 切換後有 1 秒冷卻時間防止誤觸。

## 已完成功能
1. **多模態辨識系統**：
   - 臉部：偵測 `mouthSmileLeft` 與 `mouthSmileRight` 以維持客人滿意度。
   - 雙手：將真實世界座標映射至 Canvas 上，右手為主要物理互動手，左手為輔助。
2. **階段式清理系統 (DINING_ROOM)**：
   - 分為 `CLEAR_TRASH` (撥垃圾) 與 `WIPE_TABLE` (擦桌子) 兩個階段。
   - 動態生成基於假訂單 (PASTA/DRINK) 的不同形狀物理垃圾 (`Matter.Bodies`)。
3. **洗碗判定系統 (KITCHEN)**：
   - 實作手部在盤子區域內來回移動的距離累積 (Scrubbing) 計算，達標後消除髒污。

## 待辦事項 (Todo)
- [x] 實作手部滯留辨識，以供空間切換 (Phase 2)。 ✅
- [ ] 實作 `CustomerManager`，管理客人於 `COUNTER` 與 `DINING_ROOM` 間的狀態轉移，並加入耐心扣除機制 (Phase 3)。
- [ ] 實作 `PREPARING` 階段的具體互動邏輯，並串接出餐至 `DINING_ROOM` (Phase 4)。
- [ ] 將 Canvas 中以幾何圖形繪製的物件 (垃圾、餐具、抹布、桌子背景) 替換為自訂圖片 (Image Sprite) 系統。

## 開發注意事項
- 修改繪圖函數時，務必使用 `ctx.save()` 與 `ctx.restore()`，避免污染其他渲染階段的樣式。
- 由於依賴視訊鏡頭，開發測試需在 `localhost` 或 HTTPS 環境下運行。