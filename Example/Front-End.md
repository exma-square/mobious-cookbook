#前端（Front-End）

經過本章節您會瞭解到：
* [Route] — 網址路由設定
* [Auth] — 依照使用者權限顯可操作的功能
* [View] — 畫面呈現頁面、CSS套用
* [Action] — 註冊 Action 行為、呼叫後端 RESTful API
* [Store] — 處理修改 State

在實作功能之前，先瞭解目錄結構：
app
* components
  * ∟ shared — 放置共用的 component
* data
  * ∟ en.js — 英文（多國語系）
  * ∟ fr.js — 法語（多國語系）
* utils
  * ∟ flux.js — 處理 flux 流程，包含註冊 Action 以及 Store
* postManager
  * ∟ actions — 使用者從視圖（View） 上可以使用的操作（Actions）
  * ∟ components — 元件（渲染出網頁的元件，可以想成是 View）
  * ∟ stores — Store 接收（監聽） Action 傳來的的值，並專注於處理修改狀態（State）
  * ∟ styles  — CSS 檔案

說明：postManager 資料夾處理有關 post 事件。

## 下一步
接下來建立 [文章清單](Front-End/PostList.md)，
