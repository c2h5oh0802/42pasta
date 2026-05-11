# AI Agent 開發狀態 (Project Context)

這是一款使用 HTML5 Canvas 與 MediaPipe Hands/Face 製作的第一人稱視角互動網頁遊戲，名為「42號義麵店」。

## 目前系統狀態 (Phase 5 完成)
- **零建置環境 (Zero-build setup)**：純 HTML/CSS/JS 實作，單一檔案 `index.html`。
- **UI 與存檔系統**：
  - 實作了遊戲巨觀狀態機 (Macro States)：`TITLE` -> `LEVEL_SELECT` -> `GAMEPLAY` -> `LEVEL_RESULT`。
  - 使用 `localStorage` 實作關卡解鎖與星星數存檔 (`42PastaSave`)。
  - 設計三關限時挑戰，可於上方 HUD 檢視目標金額與倒數時間。
  - **選單也支援手勢滯留導航 (UI Dwell Navigation)**，無需滑鼠即可操作遊戲流程。
- **空間狀態與導航 (Micro Locations)**：
  - 玩家可在 `COUNTER` (櫃檯)、`KITCHEN` (廚房) 與 `DINING_ROOM` (用餐區) 三個空間中切換。
- **物理與洗碗小遊戲整合**：
  - 洗碗 (`WASHING`) 邏輯已搬移至 `KITCHEN`。
  - 擦桌與撥垃圾 (`CLEANING`) 邏輯已搬移至 `DINING_ROOM`。
- **手部滯留導航 (Dwell Navigation)**：
  - 將手移至畫面邊緣的導航箭頭區域或 UI 按鈕並停留 1.2~1.5 秒，即可觸發動作。
  - 環形進度指示器 + 脈衝發光視覺反饋。
  - 觸發後有 0.8~1 秒冷卻時間防止誤觸連續點擊。

## 已完成功能
1. **多模態辨識系統**：
   - 臉部：偵測 `mouthSmileLeft` 與 `mouthSmileRight`。微笑可以**恢復櫃檯客人的耐心值**。
   - 雙手：將真實世界座標映射至 Canvas 上，全時渲染（包含選單畫面）提供沈浸式操作體驗。
2. **客人排隊與點餐系統 (CustomerManager)**：
   - 櫃檯會依據關卡設定自動產生客人排隊，且**有空位才會來新客人**。
   - 客人會依據訂單需求點餐 (🍝 香菜 / 🍝 不加香菜)，玩家需要將手停留在點餐氣泡上接單。
   - 微笑能安撫第一位客人；若耐心扣至 0，客人會氣走。氣走會**留下負評 (Negative Reviews)**，並導致後續新客人的生成速度變慢。
   - 玩家在廚房完成餐點後，系統會自動結算這筆訂單 (Mock Serve directly to customer handling flow for now)。若耐心 > 50% 玩家可直接賺錢，否則錢會掉在櫃檯必須親自去撿 (`Matter.Body`)。
3. **廚房料理系統 (Cooking Mini-game) [Phase 4]**：
   - 玩家接到訂單後前往廚房，會觸發 6 個料理步驟：
     1. 抓取生麵 (`RAW_PASTA`)
     2. 放入鍋中等待煮沸 (`BOILING`)
     3. 將熟麵撈至盤中 (`PLATE_PASTA`)
     4. 依據客人是否要香菜來抓取香菜 (`ADD_CILANTRO`) 或直接跳過
     5. 用手在盤子上方畫圈攪拌 (`STIRRING`)
     6. 將完成的盤子抓到出餐口 (`SERVE`) 結算分數
   - 料理期間手部會顯示抓取的物品，並帶有相應的 UI 提示與感應區。
4. **階段式清理系統 (DINING_ROOM)**：
   - 分為 `CLEAR_TRASH` (撥垃圾) 與 `WIPE_TABLE` (擦桌子) 兩個階段。
5. **洗碗判定系統 (KITCHEN)**：
   - 若沒有訂單，廚房會退回洗盤子模式 (`WASHING`)，實作手部在盤子區域內來回移動的距離累積。

## 待辦事項 (Todo)
- [x] 實作 `PREPARING` 階段的具體互動邏輯，包含抓麵、煮麵、加香菜與攪拌 (Phase 4)。 ✅
- [ ] 將 Canvas 中以幾何圖形繪製的物件 (垃圾、餐具、抹布、桌子背景、食材) 替換為自訂圖片 (Image Sprite) 系統。
- [ ] 串接「出餐至 `DINING_ROOM` 佔位」的機制 (目前在廚房出餐口放下後直接視為客人在櫃檯拿餐離開)。

## 開發注意事項 (防呆守則)
1. **渲染狀態隔離**：修改繪圖函數時，務必使用 `ctx.save()` 與 `ctx.restore()`，避免污染其他渲染階段的樣式。
2. **全時渲染策略**：這是一個「沒有滑鼠」的遊戲，任何非遊戲中 (如選單、結算) 的狀態，都必須**確保背景透明且持續渲染雙手**，否則玩家會完全失去操作回饋感。
3. **安全跳出 (Return Early)**：在手勢停留偵測滿 100% 觸發狀態切換後，必須立刻 `return` 或確認目標存在，不可繼續對該按鈕/卡片執行 `classList.add` 等 DOM 操作。
4. **狀態與變數初始化**：遊戲中所有的陣列（如 `trashBodies`, `stains`, `customers`, `droppedMoney`）務必在頂層就給予初始值 `[]`，不可依賴進入特定狀態時才宣告，以防 `ReferenceError` 崩潰全域。當跨越空間 (Micro Locations) 時，務必妥善加入/移除物理世界的物件。
5. **事件綁定相容性**：呼叫 HTML 元素的 `.click()` 方法在某些情境（尤其是 `div`）可能會失效，務必準備 `try-catch` 或檢查 `if (typeof el.onclick === 'function')` 優先執行直屬函數。
6. **環境需求**：由於依賴視訊鏡頭，開發測試需在 `localhost` 或 HTTPS 環境下運行。