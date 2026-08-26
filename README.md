# Handoff: 建築事務所首頁（原型結構 / AS. studio）

## Overview
建築事務所「原型結構 / AS. studio」的網站首頁與一個子頁面範例。首頁核心是一個圍繞中心標題「原型 × 未來」排列成環狀的力導向式關聯圖：每個圓點代表一個案例或理念，圓點之間以線段表示彼此的關聯性，滑鼠懸停時會將不相關的圓點與線段淡化，藉此凸顯關聯脈絡。頁面另含固定頁首（漢堡選單、Logo、導覽連結、頁內搜尋、Dark/Light 模式切換）與頁尾（聯絡資訊、社群連結）。

## About the Design Files
本資料夾內的 HTML 檔案是**設計參考稿**（design references），以原型工具產生，用來展示外觀與互動行為，並非可直接搬進正式專案的產品代碼。請將此設計在目標專案既有的技術環境（例如 React、Vue、Next.js 或其他既定框架與慣例）中**重新實作**；若專案尚無既定框架，請選擇最適合的框架後在該框架中實作這個設計。

## Fidelity
**Hifi（高保真）**：本設計為含最終顏色、字體、間距、互動行為的完整像素級稿件。請依照代碼庫既有的樣式與元件慣例，將此 UI 像素級地重現。

## Screens / Views

### 1. 首頁（建築事務所首頁.dc.html）

**Purpose**: 展示事務所所有案例／理念節點與其關聯脈絡，訪客可捲動瀏覽、懸停查看關聯、點擊跳轉到案例詳細頁（外部連結）、透過頁內搜尋快速定位節點。

**Layout**
- 整頁為單欄卷動版面，`min-height:100vh`，背景與文字顏色隨 Light/Dark 模式切換（transition 0.3s）。
- 頁首 `position:fixed`，`z-index:1000`，橫跨全寬，`padding:24px 40px`，`display:flex; justify-content:space-between; align-items:center`。
- 主要內容 `<section id="works">`，`padding:450px 12px`（上下留白讓固定頁首不遮擋內容，並讓環形圖可捲動完整顯示），內部一個正方形容器 `width:min(90vw, 86vh); max-width:2000px; aspect-ratio:1/1`，內嵌一個 `viewBox="0 0 900 900"` 的 SVG。
- 頁尾一般文件流（非 fixed），`padding:80px 40px`，內部 `max-width:1200px` 三欄 `display:flex; justify-content:space-between; align-items:flex-start`（左：聯絡資訊；中：地址；右：社群圖示）。

**Components**

**頁首（Header）**
- 左側：三條橫線的漢堡選單按鈕（36×36px，三條 22px 寬 / 2px 高橫線，8px 間距），點擊展開下拉選單（絕對定位於按鈕正下方，寬 160px，含 4 個連結：木構造／竹構造／RC構造／鋼構造，各 padding 12px 16px、字體 14px、hover 換底色），點擊選單外任意處自動關閉。
- 選單右側：事務所 Logo（`<img>`，高 40px，dark 模式套用 `filter:invert(1)`），點擊連回 `https://www.mashup.com.tw/as%20studio/`。
- 頁首正中央：頁內搜尋框，毛玻璃底（`background-color: oklch(.../ 0.15); backdrop-filter: blur(10px)`），內含放大鏡 SVG icon（20×20）+ 文字輸入框（`width:180px`，placeholder「搜尋此頁面...」），輸入時即時比對節點名稱，下方彈出最多筆數不限的結果清單（絕對定位、`max-height:280px`、可捲動），點擊結果會觸發「跳轉高亮」效果（見下方 Interactions）。
- 頁首右側：一組導覽列，毛玻璃底（同搜尋框），內含 Team／Film／Contact 三個文字連結（16px, font-weight 500, letter-spacing 0.05em）+ Dark/Light 切換按鈕（44×44px 圓角方形按鈕，border 1px solid 目前文字色；顯示太陽或月亮線稿 icon，20×20，依當前模式切換）。

**中心關聯圖（Network Diagram）**
- 座標系：SVG viewBox 900×900，圓心 (450,450)。
- 所有節點資料來自試算表：B 欄為節點名稱、A 欄為對應連結（詳見 Design Tokens 之下的完整節點清單）；C 欄以後定義節點間的連線關係。
- 節點沿一個圓環排列（環繞中心標題），環的半徑依所有節點直徑總和與固定節點間距反推，確保節點間距永遠一致（約 2.5 viewBox 單位的固定間隙）且環不會太貼近中心標題（半徑下限 420 單位）。
- 每個節點的圓點半徑依「連線數量（degree）」線性內插於 `rMin=4` 到 `rMax=24`（viewBox 單位），連線越多的節點圓點越大；額外套用 1.18 倍的視覺放大係數（`dotScale`），但不影響環形排列的節點間距與文字定位計算。
- 節點名稱以橫式書寫、單行不換行呈現在圓點外側，並整段文字旋轉朝向圓心方向（`rotate(angleDeg)` 或 `angleDeg+180`，依左右側決定文字對齊方向 `text-align: left/right`），字體大小 15px，字重依節點類型（一般 400 / 特殊分類節點 700）。
- 節點與圓心之間、以及節點彼此之間以直線（`<line>`）連接，顏色為淡灰色（Light: `oklch(0.85 0 0)`；Dark: `oklch(0.25 0 0)`），寬度 1px。
- **特殊分類節點**（11 個：原型結構、原型建築、原型教學、原型獲獎、原型數位、原型展覽、原型建築+原型結構、構technics、法structure data、章architecture design、成construction）文字與圓點顏色：
  - Light 模式：`#9B9B9B`；懸停於節點本身時變為 `#000000`（黑色）
  - Dark 模式：`#BDA434`
  - 皆為粗體（font-weight 700）
- 一般節點顏色：Light `oklch(0.16 0 0)`（近黑）；Dark `oklch(0.95 0 0)`（近白）。
- 中心標題：`原型 × 未來`（Noto Serif TC，62px，font-weight 600，letter-spacing 0.02em）+ 副標「Architecture + Structure」（22px，letter-spacing 0.06em，opacity 0.55）。
- **投射光效果（Spotlight）**：從「原型結構」節點朝中心標題方向，疊加一個窄→寬的錐形線性漸層多邊形（`<polygon>` + `<linearGradient>`），營造光束灑落在中心字樣上的效果。Light 模式色彩接近白的淺灰（`#EDEDED`，峰值不透明度 0.55）；Dark 模式為白色（峰值不透明度 0.045，即 5% 以下）。此圖層置於連線之下。

**頁尾（Footer）**
- 背景色：Light `oklch(0.95 0 0)`；Dark `oklch(0.12 0 0)`。
- 左：電話 06-2905293／傳真 06-2905493／電郵 as.studio001@gmail.com（`mailto:` 連結），皆左對齊、14px。
- 中：地址「台南市東區中華東路三段44巷7號1樓」，置中對齊、14px。
- 右：Facebook／Instagram／YouTube 三個圓形圖示按鈕（32×32px 圓形，內含 18×18 SVG 線稿 icon），`target="_blank" rel="noopener"`，hover 時 `opacity:0.7`。

### 2. 子頁面範例（原型教學.dc.html）
**Purpose**: 示範案例/理念節點點擊後導向的詳細頁面樣板（此範例為「原型教學」主題，內容為佔位文字，之後需依實際內容填充）。

**Layout**: 沿用首頁完全相同的頁首與頁尾設計與功能（僅移除搜尋框，因子頁無需要跨節點搜尋）；內容區 `max-width:900px`，`padding:220px 40px 160px`，含麵包屑導覽（首頁 / 原型教學）與大標題（Noto Serif TC, 48px, font-weight 600）。

**Hover-Expand 圖片列（麵包屑上方）**：橫排的滑塊式圖片列（skiper35「Hover Expand」效果的替代實作，純 CSS/JS，無 React/Framer Motion 依賴），每個滑塊代表一個項目（欄位：編號／大標／小標），內容透過後台管理工具維護（見 [CLAUDE.md](./CLAUDE.md)）。

- 版面：`display:flex`，各滑塊預設等寬（`flex:1`），滑塊之間以 0.5px 分隔線區隔（Light: `rgba(136,136,136,0.8)`；Dark: `rgba(255,255,255,0.8)`），第一個滑塊左側無分隔線。
- 互動：滑鼠懸停的滑塊展開變寬（`flex:4`，`transition:flex 0.55s cubic-bezier(.16,1,.3,1)`），其餘滑塊維持等寬不變；收合時只顯示編號（如「01」），展開後才顯示完整圖說。
- 底色：跟隨 Dark/Light 模式（Dark 黑 `#0a0a0a`／Light 白 `#ffffff`），圖片目前為佔位圖示（線稿 SVG，之後替換為實際案例圖片）。
- **展開後必為正方形（1:1）**：這是固定規則，套用在任何子頁面、不管放幾個滑塊都要成立。因為滑塊寬度由 flex 動態決定（會隨視窗寬度、滑塊數量而變），不能寫死固定 px 高度，必須用 JS 在執行期即時量測換算：
  - 公式：`展開後寬度 = 圖片列實際寬度 × HOVER_FLEX ÷ (HOVER_FLEX + (滑塊總數 − 1))`（`HOVER_FLEX` 為懸停滑塊的 `flex` 值，需與 CSS 的 `.sc-gallery-panel:hover { flex:__; }` 保持一致，目前是 4；其餘滑塊皆為 `flex:1`）
  - 算出的「展開後寬度」直接設成整排滑塊的高度（`.sc-gallery` 的 `height`），確保懸停展開時寬高相等
  - 有照片的滑塊，`background-size` 也要同步設成這個正方形尺寸（配合 `background-position:left center`，讓收合時只看到照片左側一截、懸停展開後完整露出，圖片本身不隨懸停切換或重新縮放）
  - 監聽 `window resize` 重新計算，避免視窗尺寸改變後跑掉
  - 實作見 `原型教學.dc.html` 的 `Component.componentDidMount()` 內 `_sizeGallerySquare()`，是滑塊數量無關的通用函式，直接複製到其他子頁面即可沿用
- **圖說排版規格**（展開狀態，見下方尺寸圖）：
  - 左側編號（如「01」）：Noto Serif TC Extra Bold（800），字級與「小標題＋大標題」兩行疊放的總高度切齊。
  - 右側上下兩行：上方**小標題**（原文=試算表「小標」欄，英文譯名）為全排版中最細的字重（Noto Serif TC Extra Light 200，13px）；下方**大標題**（原文=試算表「大標」欄，中文名稱）為粗體（Noto Serif TC Extra Bold 800，16px），與編號同粗細。
  - 圖說整體寬度固定等於該滑塊展開後的寬度；標題文字過長時**自動換行**，並**自動縮小字體**以維持在滑塊寬度內（不得截斷/省略號處理）。目前實作為 `white-space:nowrap` + `overflow:hidden`（截斷），尚未實作自動換行/縮字，待有較長標題內容時再補上。

## Interactions & Behavior

### Hover 高亮
滑鼠懸停在任一節點的圓點或文字上時：
- 該節點與其相連的節點、連線維持 `opacity:1`
- 其餘所有節點與連線淡化為 `opacity:0.3`
- transition: `opacity 0.25s ease`

### 節點連結跳轉
每個節點外層包一個 `<a href>`，點擊直接開新的目標網址（同一分頁導頁）；若試算表 A 欄無對應網址，`href="#"`（暫不跳轉）。

### 頁內搜尋
- 輸入關鍵字即時（`onInput`）比對所有節點名稱（不分大小寫、`includes` 比對），顯示結果下拉清單。
- 點擊清單外任一處會關閉建議清單。
- 點擊建議清單中的任一結果，觸發「跳轉高亮」動畫：
  1. 在目標節點位置疊加一個實心黃色圓形高亮（`radial-gradient`，帶模糊/羽化邊緣），半徑略大於畫面中最大的節點圓點，確保不遮擋任何節點本體，z-index 置於節點下層。
  2. 若目標節點目前不在可視範圍內，改為在畫面邊緣顯示一個扁平等腰三角形指標（`clip-path` 三角形，寬52px高26px，帶輕微縮放脈動動畫 `arrowPulse`），角度隨捲動即時計算並指向節點方向。
  3. 節點進入可視範圍後，三角形淡出、圓形高亮淡入，並持續跟隨節點位置（每禎 `requestAnimationFrame` 更新座標）。
  4. 使用者點擊畫面任意處後，高亮圓形淡出消失（`opacity` transition 0.45s 後移除）。

### Dark / Light 模式切換
點擊頁首右上角按鈕，切換全站配色：
- Light: 背景 `oklch(0.99 0 0)`（近白）、文字 `oklch(0.16 0 0)`（近黑）
- Dark: 背景 `oklch(0.15 0 0)`（近黑）、文字 `oklch(0.95 0 0)`（近白）
- 所有顏色相關樣式皆帶 `transition: background-color 0.3s, color 0.3s` 或 `filter 0.3s`（Logo invert）。
- 按鈕內 icon 依模式切換（Dark 顯示太陽線稿 icon 表示可切回 Light；Light 顯示月亮線稿 icon 表示可切至 Dark）。

### 漢堡選單（左上角三線按鈕）
點擊展開／收合「木構造／竹構造／RC構造／鋼構造」四個連結的下拉選單；點擊選單外任一處自動關閉。

## State Management
- `hoveredId`: 目前懸停中的節點 id（用於連線/節點淡化計算）
- `isDarkMode`: 布林，控制全站配色
- `showMaterialMenu`: 布林，控制漢堡選單下拉開關
- `showSearchModal` / `searchInput` / `searchResults`: 搜尋框輸入與比對結果
- `flashId`: 目前正在播放跳轉高亮動畫的節點 id
- 節點與連線資料（`nodeData`、`edgeData`）為靜態陣列，直接寫在元件內；正式專案建議改為從 CMS 或資料檔載入，維持相同的資料結構（`{id, name, href}` 節點陣列 + `[idA, idB]` 連線陣列）。

## Design Tokens

**Colors**
- Light 背景：`oklch(0.99 0 0)`；Dark 背景：`oklch(0.15 0 0)`
- Light 文字：`oklch(0.16 0 0)`；Dark 文字：`oklch(0.95 0 0)`
- Light 連線：`oklch(0.85 0 0)`；Dark 連線：`oklch(0.25 0 0)`
- 特殊分類節點 Light：`#9B9B9B`（懸停自身時 `#000000`）；Dark：`#BDA434`
- 投射光 Light：`#EDEDED`（峰值不透明度 0.55）；Dark：`#FFFFFF`（峰值不透明度 0.045）
- 跳轉高亮：黃色 `#FFD400`／`rgba(255,208,0, …)`
- 頁尾社群圖示底色：Light `oklch(0.9 0 0)`；Dark `oklch(0.25 0 0)`

**Typography**
- 主要無襯線字體：`'Helvetica Neue', Helvetica, Arial, 'PingFang TC', 'Microsoft JhengHei', sans-serif`
- 中心標題 / 頁面大標：`'Noto Serif TC', 'Source Han Serif TC', serif`（Google Fonts: Noto Serif TC weight 500/600，首頁 `建築事務所首頁.dc.html` 使用）
- 節點標籤字體大小：15px；中心標題：62px（首頁）；導覽連結：16px；頁尾文字：14px

**子頁面內容字級系統（`原型教學.dc.html` 及後續子頁面樣板適用）**

全部使用 `'Noto Serif TC', 'Source Han Serif TC', serif`（Google Fonts: Noto Serif TC weight 200/700/800），中文與英文（原文/譯文並列）套用相同字級規格：

| 層級 | 中文 | 英文 |
| --- | --- | --- |
| 大標題（頁面主標題，如 `<h1>`） | Noto Serif TC Extra Bold（800）15px | Noto Serif TC Extra Bold（800）15px |
| 子標題（各段落小標題） | Noto Serif TC Bold（700）13px | Noto Serif TC Bold（700）13px |
| 內文（段落內容） | Noto Serif TC Extra Light（200）13px | Noto Serif TC Extra Light（200）13px |

目前 `原型教學.dc.html` 的大標題（`原型教學`）僅有中文版本，英文版本待補。

**Spacing / Sizing**
- 節點圓點半徑：4–24 viewBox 單位（依連線數量線性內插），視覺放大係數 1.18
- 節點間固定間距：約 2.5 viewBox 單位
- 環半徑下限：420 viewBox 單位（viewBox 900×900，圓心 450,450）
- 頁首 padding：24px 40px；頁尾 padding：80px 40px
- 頁內搜尋輸入框寬：180px；漢堡選單下拉寬：160px；搜尋結果下拉寬：230px

**Border radius**: 按鈕/面板 4–8px；社群圖示按鈕 50%（圓形）

## Assets
- Logo 圖檔：`uploads/logo_20210310101340.png`（已去背，隨模式套用 `invert` filter）
- 所有 SVG icon（放大鏡、太陽、月亮、Facebook、Instagram、YouTube）為內嵌線稿 SVG，無外部圖檔依賴。

## Files
- `建築事務所首頁.dc.html` — 首頁完整原型（含頁首/關聯圖/頁尾）
- `原型教學.dc.html` — 子頁面樣板範例
- `support.js` — 原型工具的執行期腳本（僅供本原型於瀏覽器直接開啟用，正式專案不需要此檔）
- `uploads/` — Logo 等圖片資源
