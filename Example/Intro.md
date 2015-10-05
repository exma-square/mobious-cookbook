# 實作範例 ─ 以Post Manager為例

這篇實作範例我們會帶大家瞭解 Mobious 的架構及流程，並且如何把常見的 Post Manager 功能實際應用在 Mobious。
在實作的過程中會先從 Post Manager 功能敘述帶家家了解後，並分兩章節：後端（Back-End）與前端（Front-End）作說明。

## Post Manager 功能說明
##### 一般使用者
* 顯示文章清單
* 檢視文章內容

##### Admin 權限
* 指定文章編輯者（Editor）

##### Editor 權限
* 新增文章
* 修改文章
* 刪除文章

## 後端（Back-End）範例包含
後端（Back-End）會以架構導向實作。
* [Model（sequelize）](Back-End/Model.html) — 定義資料表型別及設定關聯性
* [Spec](Back-End/Spec.html) — 定義規格及測試
* [Controller](Back-End/Controller.html) — 處理邏輯層
* [Route](Back-End/Route.html) — RESTful API 路由設定


## 前端（Front-End）範例包含：
前端（Front-End）會以功能導向實作。
* [文章清單](Front-End/PostList.html)
* [文章瀏覽](Front-End/PostSingle.html)
* [建立文章](Front-End/PostCreate.html)
* [修改、刪除文章](Front-End/PostEditDelete.html)
* [Admin 指派 Editor](Front-End/AdminSelectEditor.html)

以上功能都含括：
* [Route] — 網址路由設定
* [Auth] — 依照使用者權限顯可操作的功能
* [View] — 畫面呈現頁面、CSS套用
* [Action] — 註冊 Action 行為、呼叫後端 RESTful API
* [Store] — 處理修改 State

> 建議閱讀方式為從上依序往下閱讀

## 專有名詞
| English | 中文翻譯 |
| --- | --- |
| View | 視圖 |
| Action | 操作 |
| Store | 資料處理器 |
| State | 狀態 |
| Component | 元件 |
| Render | 渲染 |
| Route | 路由 |

## 下一步
瞭解完大致上的 Post Manager 架構後，接下來我們就要開始從 [後端（Back-End）Model](Back-End/Model.html) 開始撰寫 。
