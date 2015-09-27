# API Route
##安裝
`npm install koa-router --save`

##使用
* 引用 `koa-router`
* 安裝中間件
```
var app = require('koa')();
var router = require('koa-router')();
```

設定 GET，POST，PUT，DELETE 方式：
```
var app = require('koa')();
var router = require('koa-router')();

router
  .get('/', function *(next) {
    console.log('get');
    this.body = 'Home';
  })
  .post('/users', function *(next) {
    console.log('post');
  })
  .put('/users/:id', function *(next) {
    console.log('put');
  })
  .del('/users/:id', function *(next) {
    console.log('del');
  });

app.use(router.routes());

app.listen(3000);

```

路由名稱修改：
```
router.get('user','/users/:id', function *(next) {
    this.body = this.params.id;
  })

router.url('user','test')
```


可以用 `this.params` 來取得值：
```
router.get('user','/users/:id', function *(next) {
    this.body = this.params.id;
  })
```

##參考資料
* [koa-router Github](https://github.com/alexmingoia/koa-router)

## 下一步
[Model ─ Sequelize](Model.md)
