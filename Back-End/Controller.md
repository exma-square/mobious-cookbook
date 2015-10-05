# Controller
在此章節，會先讓您對 mobious 後端 Controller 架構方向有個概念性瞭解，並不會太深入去講解程式碼，詳細程式碼的講解會留到
[Example](../Example/Intro.html) 實作才會做做深入探討。

在 mobious 後端的 Controller 會使用到 koa-route 以及 Sequelize ，因此會先建議您閱讀以下兩章節
* [Model ─ Sequelize](Model.html)
* [API Route](API-Route.html)

mobious 後端的目錄架構：

server
* controllers
  * ∟ index.js — 定義後端所有路由，並設定連結到的函數（Generator function）
  * ∟ user.js — 使用者（新增，讀取）
  * ∟ post.js — 文章（讀取）

建立使用者：
檔案：`user.js`
```
exports.index = function *() {

  let users = yield models.User.findAll()

  this.body = {users}
};
exports.create = function *() {

  let newUser = this.request.body;

  let result = null;

  try {
    result = yield models.User.create(newUser);
  } catch (e) {
    console.error("create user error", e);
  }

  let user = result;

  this.body = {user}
};
```


讀取單一文章：
`post.js`
```
exports.get = function *() {

  let postId = this.params.id;
  let post = yield models.Post.find({
    where: {
      id: postId
    },
    include: [ { model: models.Tag ,attributes: ['name']} ]
  });

  this.body = {post}
};
```

設定 Route 位置並傳入一個 Generator function 的 middleware。
`index.js`
```
...略
import UserController from './user';
import PostController from './post';
import Router from 'koa-router';

var publicRoute = new Router()

publicRoute.get('/rest/user/', UserController.index);
publicRoute.get('/rest/post/:id', PostController.get);
this.router.post('/rest/user/', UserController.create);

```

## 下一步
[Seeding](Seeding.html)
