# Mocha ─ API Testing
mobious 使用 mocha 來作為測試的框架。

#Mocha 介紹

Mocha 是 Node.js 的其中一套測試框架，支援 BDD（Behavior Driven Development） TDD（Test Driven Development）， 這邊我們會偏向使用 BDD 的方式來做，每個 test cast 都會有一個 describe()，來敘述測試的情境（scenario）， 之後在把驗證結果放在 it() 裡。

###安裝
`npm install -g mocha`

###使用

官網範例：
```
var assert = require("assert");
describe('Array', function() {
  describe('#indexOf()', function () {
    it('should return -1 when the value is not present', function () {
      assert.equal(-1, [1,2,3].indexOf(5));
      assert.equal(-1, [1,2,3].indexOf(0));
    });
  });
});
```
以上屬於 BDD（Behavior Driven Development）測試方式，describe() 函式用來敘述測試的情境並且可以層層嵌套，
像是以上的 Array 及  #indexOf() ，而 it() 函式裡則是會放具體的測試驗證邏輯。


如果要走非同步可以在函數內加上 done() 表示結束：
```
describe('User', function() {
  describe('#save()', function() {
    it('should save without error', function(done) {
      var user = new User('Luna');
      user.save(function(err) {
        if (err) throw err;
        done();
      });
    });
  });
});
```
在驗證完成後在使用 done() 來告知 mocha 這個測試已經完成。
> 這邊要注意的是每個 it() 中只能有一個 done()

如果有個別要獨立測試可以加上 only()：
```
describe('Array', function() {
  describe('#indexOf()', function() {
    it.only('should return -1 unless present', function() {
      // ...
    });

    it('should return the index when present', function() {
      // ...
    });
  });
});
```

要略過則可以使用 skip()：
```
describe('Array', function() {
  describe('#indexOf()', function() {
    it.skip('should return -1 unless present', function() {
      // ...
    });

    it('should return the index when present', function() {
      // ...
    });
  });
});
```

另外如果要在測試之前或之後執行的可以撰寫在 before() ， after() 中：
```
describe('hooks', function() {

  before(function() {
    // runs before all tests in this block
  });

  after(function() {
    // runs after all tests in this block
  });

  beforeEach(function() {
    // runs before each test in this block
  });

  afterEach(function() {
    // runs after each test in this block
  });

  // test cases
});
```
* beforeEach() ， afterEach() 則是只會對應到他的子測試。
* before() ， after() 再宣告時的順序要求。

# mobious API Testing
在 mobious 中的測試目錄架構如下：

test
* server
  * controllers
    * ∟ post.spec.js — post（文章）測試
    * ∟ user.spec.js — user（使用者）測試

執行測試：
```
npm run rest-test
```

針對要測試的案例加上 only()

檔案：`user.js`
```
describe("User", () => {

  it.only("index all user", (done) => {

    request.get("/rest/user/")
    .expect(200)
    .end((error, res) => {
      console.log("res.body.users", res.body.users);

      res.body.users.should.be.Array;
      res.body.users[0].id.should.greaterThan(0);

      done(error);
    });
  });

  it("update user activated", (done) => {

    //Setting is 'admin' authority
    sinon.stub(services.user, 'getAuthStatus', (app) =>{
      return {authority: 'admin'};
    });

    let editUserId = 2;
    let editActivated = {activated: true};

    request.put("/rest/user/activated/" + editUserId)
    .expect(200)
    .send(editActivated)
    .end((error, res) => {
      models.User.find({
        where: {
          id: editUserId
        }
      }).then((updatedUser)=>{
        updatedUser.activated.should.be.equal(editActivated.activated);
        done();
      });
    });


  });

});

```
以上程式碼說明會在 [Example](../../Example/Intro.html) 章節內會在詳細說明，此章節只帶大家瞭解 mobious 執行測試的方式。

##參考資料
* [mochajs - 官網](https://mochajs.org/)


## 下一步
[介紹 ─ 以Post Manager為例](../../Example/Intro.html)
