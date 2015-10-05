# Koa 介紹

[Koa.js](https://github.com/koajs/koa/) 是 Node.js 下一代的 web 框架，是由 [Express](https://github.com/strongloop/express) 及 [Connect](https://github.com/senchalabs/connect) 的團隊所撰寫,且 [Koa.js](https://github.com/koajs/koa/) 是一個分常輕量框架只有550行程式碼。

[Koa.js](https://github.com/koajs/koa/)  大量運用到利用了 ES6 裡 Generator 新特性，Generator 可以減輕開發人員在流程管理上有更多的彈性，
因為以往 JavaScript 在有很多 callback 時會成很多層的嵌套高山，會造成讓開發人員在處理上會難以掌握。

##安裝
`npm install koa --save`

##使用
先來一個基本的 Hello World（你好世界）：
```
var app = require('koa')();

app.use(function *(){
  this.body = 'Hello World';
});

app.listen(3000);
```
`app.use(function)` 是將function 當做中間件（Middleware）傳遞到 app 中。

如果對 Generator 還不熟悉，可能會發現到上述有一個 `function *`，因此先來帶大家對 Generator 有初步的瞭解。
## Generator

跟以往的 `function` 函數不同的是， Generator 函數會是以 `function *` 有 * 開頭關鍵字，並且在函數內如有遇到 `yield` 會暫停且返回物件，
返回的物件的 value 屬性則是 `yield` 右側的結果：
```
function * demo() {
  console.log('1');
  yield "2";
  console.log('demo done');
}
```

如果直接呼叫這個函數並不會執行，因為他是回傳一個 Generator 物件：
```
var app = demo();
```


如要執行則要使用 Generator 物件的方法 `next()`：
```
function * demo() {
  console.log('1');
  yield "2";
  console.log('demo done');
}

var app = demo();
var a = app.next();
console.log(a);
console.log('3');

// 顯示 1
// 顯示 { value: '2', done: false }
// 顯示 3
```

可以發現 demo done 的字串目前還沒被印出來，就如同之前上述所說的當 Generator 函數中碰到 `yield` 就會暫停且返回物件，
這時候我們可以在呼叫一次 `next()`：
```
function * demo() {
  console.log('1');
  yield "2";
  console.log('demo done');
}

var app = demo();
var a = app.next();
console.log(a);
console.log('3');

var b = app.next();
console.log(b);


// 顯示 1
// 顯示 { value: '2', done: false }
// 顯示 3
// 顯示 demo done
// 顯示 { value: undefined, done: true }
```
>另外可以用 done 屬性確認該函式（使範例是demo）是否已經執行結束。

## Koa Middleware（中間件）
在 Koa.js 裡的 Middleware 都是 Generator function，優點是我們可以靈活的在 Middleware 中切換到其他的 Middleware。
```
var koa = require('koa')();

koa.use(function* (next) {
    console.log('A');
    yield next;
});

koa.use(function* (next) {
    console.log('B');
    yield next;
    console.log('C');
});

koa.use(function* () {
    console.log('D');
    this.body = 'E';
    console.log('F');
});

koa.listen(3000);

```
如果你可以掌握以上程式碼的印出順序，代表你已經瞭解如何在 Koa.js 控制流程了。

答案(請反白)：『<font color='white'>A，B，D，F，C</font>』

在 Koa.js 的官網也有一張說明流程圖，非常的清楚也可以做參考[來源連結](http://koajs.in/doc/Middleware/guide/)：
![Middleware](../Image/middleware.gif "Middleware")

##參考資料
* [Koa.js Github](https://github.com/koajs/koa/)
* [Koa.js Middleware（中間件）](https://github.com/koajs/koa/wiki#Middleware)

## 下一步
使用 koa-route 設定路由 [API Route](API-Route.html)
