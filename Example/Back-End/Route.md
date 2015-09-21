# Route


前篇文章 Contorller 完成以後，這邊就可以引入剛剛的 Controller

```import PostController from './post';```

以及這邊使用的是 `koa-router`

```import Router from 'koa-router';```


檔案路徑：`server/contorllers/index.js`

```
var publicRoute = new Router()
setupPublicRoute() {
  publicRoute.get('/rest/post/', PostController.index);
  publicRoute.get('/rest/post/:id', PostController.get);
  app.use(publicRoute.middleware())
}
```

另外這邊大家應該有發現 Create，Update，Delete 的方法怎麼會沒有寫在這邊？
因為 Create，Update，Delete 這些方法算是都需要驗證登入過的使用者才能使用。


因此我們把他抽離出來，當有驗證登入過得使用者才能使用這些 URI 如下：


```
setupAppRoute() {

  this.router.put('/rest/post/updateEditor/:id', PostController.updateEditor);
  this.router.put('/rest/post/:id', PostController.update);
  this.router.delete('/rest/post/:id', PostController.delete);
  this.router.post('/rest/post/', PostController.create);
  this.router.post('/rest/post/fileUpload/', PostController.upload);

  this.app.use(this.router.middleware())
}
```

### 完整程式碼
```
import PostController from './post';
import Router from 'koa-router';

var publicRoute = new Router()
setupPublicRoute() {
  publicRoute.get('/rest/post/', PostController.index);
  publicRoute.get('/rest/post/:id', PostController.get);
  app.use(publicRoute.middleware())
}

app.use(function*(next) {
  if (true || services.user.isAuthenticated(this)) {
    yield next
    } else {
      this.redirect('/auth/login')
    }
})

setupAppRoute() {
  this.router.put('/rest/post/updateEditor/:id', PostController.updateEditor);
  this.router.put('/rest/post/:id', PostController.update);
  this.router.delete('/rest/post/:id', PostController.delete);
  this.router.post('/rest/post/', PostController.create);
  this.router.post('/rest/post/fileUpload/', PostController.upload);

  this.app.use(this.router.middleware())
}
```

### 程式碼說明：
* 在 `  if (true || services.user.isAuthenticated(this))` 驗證過後執行 `yield next`，
這邊指的檔案是在 `server/koa.js` ：
```
controllers.setupPublicRoute()
controllers.setupAppRoute()
```
當 `setupPublicRoute()` 內的方有 next 才會繼續往下執行 `setupAppRoute()`，因此可以避免到未登入者連到 `setupAppRoute()` 內所設定的 URI。

## 下一步
後端（Back-End）已經完成了，讓我們在繼續學習 [前端（Front-End）](../Front-End.md) 。
