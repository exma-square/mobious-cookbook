# 實作範例 ─ 以Post Manager為例

這篇實作範例我們會帶大家瞭解 Mobious 的架構及流程，並且如何把常見的 Post Manager 功能實際應用在 Mobious。
在實作的過程中會先從 Post Manager 功能敘述帶家家了解後，再分兩章節後端（Back-End）與前端（Front-End）說明：

## Post Manager 功能說明
##### 一般使用者
* 顯示文章清單
* 檢視文章內容
* 登入

##### Admin 權限
* 指定文章編輯者（Editor）

##### Editor 權限
* 新增文章
* 修改文章
* 刪除文章

## 後端（Back-End）範例包含：
* [Model（sequelize）](Back-End/Model.md) — 定義資料表型別及設定關聯性
* [Spec](Back-End/Spec.md) — 定義規格及測試
* [Route](Back-End/Route.md) — RESTful API 路由設定
* [Controller](Back-End/Controller.md) — 處理邏輯層

## 前端（Front-End）範例包含：
* [Route](Front-End/Route.md) — 網址路由設定
* [Auth](Front-End/Auth.md) — 依照使用者權限顯可操作的功能
* [View](Front-End/View.md) — 畫面呈現頁面、CSS套用
* [Action](Front-End/Action.md) — 註冊 Action 行為、呼叫後端 RESTful API
* [Store](Front-End/Store.md) — 處理修改 State

## 下一步
瞭解完大致上的 Post Manager 架構後，接下來我們就要開始從 [後端（Back-End）Model](Back-End/Model.md) 開始撰寫。
