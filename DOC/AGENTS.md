# 42號義麵店 - 開發者交接手冊 (AGENTS.md)

## 1. 專案概況
本專案為一款基於瀏覽器、利用 MediaPipe 臉部與手勢偵測進行互動的經營類遊戲。
- **核心技術**：Mediapipe (Face Landmarker, Hand Landmarker), Matter.js (物理垃圾與金錢), HTML5 Canvas.
- **操作核心**：微笑接單、手勢滯留導航、模擬物理搓洗/料理。

## 2. 核心規範 (不可違反的準則)

### 🚨 偵測邏輯解耦 (非常重要)
- **錯誤紀錄**：曾將微笑判定放在 `handleHandLogic` 中，導致玩家沒伸手就無法接單。
- **規範**：`handleFaceLogic` (微笑、臉部) 與 `handleHandLogic` (手勢、抓取) 必須**完全獨立調用**。嚴禁在一個偵測函數中使用另一個偵測結果作為「提早返回 (return)」的條件。

### 🗺️ 空間導航與感應對齊
- **雙重更新規則**：修改導航箭頭位置時，必須**同時修改**以下兩處：
  1. `drawNavigationArrows()`：控制視覺上的三角形與文字位置。
  2. `getDynamicNavZones()`：控制手勢滯留感應的 Hit Box。
- **遮擋避讓**：頂部中央區域 (x: 130~830, y: 0~150) 預留給「視覺化訂單卡片」。
  - **洗手台 (SINK)** 回廚房的導航必須設在 **左側**。
  - **廚房 (KITCHEN)** 的提示文字必須設在 **左上角**。

### 🍝 料理與出餐狀態管理
- **狀態清理**：出餐成功後，必須手動重置所有盤子內容布林值 (`plateHasNoodles`, `plateHasCement`, `plateHasCilantro`)。
- **盤子資源**：當 `cleanPlates === 0` 時，應自動隱藏廚房中的盤子底座視覺，並阻斷料理起始邏輯。

## 3. UI 佈局規範 (座標參考)
- **頂部中央**：`#order-container` (HTML Overlay)，用於顯示 Overcooked 風格訂單。
- **左上角**：料理步驟提示文字 (Canvas 繪製，x: 20)。
- **左下角**：`#status-hud` (HTML) 與 `#plate-display` (HTML)，顯示分數、時間、剩餘盤子。
- **中心區域**：物理交互區 (鍋子、盤子、洗碗區)。

## 4. 常見 Bug 與修復紀錄 (供未來 AI 參考)
- **SyntaxError**：注意 `renderGame` 中的巢狀 `if-else`。若修改空間渲染邏輯，務必檢查括號對齊。
- **全螢幕偏移**：所有座標判定必須通過 `scaleX`, `scaleY` 轉換，不可直接使用 `clientX/Y`。
- **接單無效**：檢查微笑門檻 (目前設為 0.2)。若無效，先檢查 `isSmiling` 是否在 `renderGame` 中觸發了綠色指示燈。

## 5. 未來擴展建議
- 替換目前的 `ctx.arc/fillRect` 為 `Image` 物件 (Sprites)。
- 加入音效系統 (煮麵聲、洗碗聲)。
- 增加更多配料種類 (目前僅有香菜與混泥土)。