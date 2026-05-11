# AI Agent 開發狀態 (Project Context)

這是一款使用 HTML5 Canvas 與 MediaPipe Hands/Face 製作的第一人稱視角互動網頁遊戲，名為「怪洨餐館」。

## 目前系統狀態 (MVP Version)
- **零建置環境 (Zero-build setup)**：純 HTML/CSS/JS 實作，單一檔案 `index.html`。
- **物理引擎**：使用 `Matter.js` 處理桌面上的垃圾與碰撞判定。
- **視覺辨識**：使用最新的 `@mediapipe/tasks-vision` 同時處理手部 (HandLandmarker) 與臉部表情 (FaceLandmarker)。
- **第一人稱視角**：將攝影機影像作為暗色背景，並將 MediaPipe 的手部骨架轉化為第一人稱伸出的虛擬手臂與發光特效。

## 已完成功能
1. **多模態辨識系統**：
   - 臉部：偵測 `mouthSmileLeft` 與 `mouthSmileRight` 以維持客人滿意度。
   - 雙手：將真實世界座標映射至 Canvas 上，右手為主要物理互動手，左手為輔助。
2. **階段式清理系統 (CLEANING)**：
   - 分為 `CLEAR_TRASH` (撥垃圾) 與 `WIPE_TABLE` (擦桌子) 兩個階段。
   - 動態生成基於假訂單 (PASTA/DRINK) 的不同形狀物理垃圾 (`Matter.Bodies`)。
   - 實作「將垃圾推落桌子邊緣」的判定邏輯。
   - 當垃圾清空後，雙手自動轉換為海綿抹布圖示，進入擦拭污漬階段。
3. **洗碗判定系統 (WASHING)**：
   - 實作手部在盤子區域內來回移動的距離累積 (Scrubbing) 計算，達標後消除髒污。

## 待辦事項 (Todo)
- [ ] 將 Canvas 中以幾何圖形繪製的物件 (垃圾、餐具、抹布、桌子背景) 替換為自訂圖片 (Image Sprite) 系統。
- [ ] 補齊 `ORDERING` 階段的具體互動邏輯與 UI 呈現。
- [ ] 補齊 `PREPARING` 階段的互動邏輯 (如調配義麵或搖晃飲品)。

## 開發注意事項
- 修改繪圖函數時，務必使用 `ctx.save()` 與 `ctx.restore()`，避免污染其他渲染階段的樣式。
- 由於依賴視訊鏡頭，開發測試需在 `localhost` 或 HTTPS 環境下運行。