# AI Agent 開發狀態 (Project Context)

這是一款使用 HTML5 Canvas 與 MediaPipe Hands/Face 製作的第一人稱視角互動網頁遊戲，名為「42號義麵店」。

## 當前開發進度 (Phase 5.5)
- [x] **客流系統優化**：客人點餐後自動入座，釋放櫃檯。
- [x] **靈活料理流程**：實作 `ASSEMBLY` 自由組裝階段，可先放配料再放麵，鍋子與盤子邏輯解耦。
- [x] **空間導航重構**：洗手台 (`SINK`) 移至廚房下層區域，建立嚴格的空間層級。
- [x] **全螢幕 UI 支持**：實作 `object-fit: contain` 與動態座標縮放補償。
- [x] **洗滌系統升級**：門檻調降、進度跨空間保留、加入「盤子補滿」提示。

## 核心架構說明
- **空間拓撲**：
    - `COUNTER` ↔ `KITCHEN` ↔ `SINK`
    - `COUNTER` ↔ `DINING_ROOM`
- **座標轉換**：全螢幕模式下，所有 HTML UI 的 `getBoundingClientRect` 必須乘上 `GAME_WIDTH / canvas.offsetWidth` 進行補償。
- **料理狀態**：使用 `potNoodleState` 與 `plateHasNoodles/Cement/Cilantro` 布林值追蹤物理內容，而非純線性狀態機。

## 已完成功能
1. **多模態辨識系統**：
   - 臉部：偵測 `mouthSmileLeft` 與 `mouthSmileRight`。微笑可以**恢復櫃檯客人的耐心值**。
   - 雙手：將真實世界座標映射至 Canvas 上，全時渲染（包含選單畫面）提供沈浸式操作體驗。
2. **客人排隊與點餐系統 (CustomerManager)**：
   - 櫃檯會依據關卡設定自動產生客人排隊，且**有空位才會來新客人**。
   - 客人會依據訂單需求點餐 (🍝 香菜 / 🍝 不加香菜)，玩家需要將手停留在點餐氣泡上接單。
3. **廚房料理系統 (Cooking Mini-game)**：
   - 玩家接到訂單後前往廚房，採自由組裝制：
     - 鍋子煮麵：抓麵 -> 煮麵 -> 熟麵
     - 盤子組裝：自由加入熟麵、混泥土、香菜
     - 攪拌與出餐：滿足訂單需求後自動觸發攪拌。
4. **階段式清理系統 (DINING_ROOM)**：
   - 分為 `CLEAR_TRASH` (撥垃圾) 與 `WIPE_TABLE` (擦桌子) 兩個階段。
5. **洗碗判定系統 (SINK)**：
   - 位於廚房內側，實作手部在盤子區域內來回移動的距離累積。

## 開發防呆守則
1. **巢狀結構檢查**：在修改 `renderGame` 中的空間判斷時，務必確認 `microLocation` 的 `if-else` 對齊。
2. **座標一致性**：手勢感應區 (`NAV_ZONES`) 必須透過 `getDynamicNavZones()` 動態獲取，以確保與視覺箭頭同步。
3. **出餐清理**：當 `dragItem` 為 `FINISHED_PLATE` 時，必須隱藏桌面上的盤子視覺。
4. **渲染狀態隔離**：修改繪圖函數時，務必使用 `ctx.save()` 與 `ctx.restore()`，避免污染其他渲染階段的樣式。
5. **全時渲染策略**：確保背景透明且持續渲染雙手，否則玩家會失去操作回饋感。