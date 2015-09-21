# Spec 前言
撰寫 Post Spec 之前可以先列出大致上會有幾種行為要寫：
* `取得文章清單`
* `取得單一文章`
* `建立文章`
* `刪除文章`
* `更新文章`
* `更新編輯者`
* `上傳文章圖片`

之後可以依功能情境放在同一個描述中：
所以會變成：
* 取得
  * `取得文章清單`
  * `取得單一文章`
* 建立刪除
  * `建立文章`
  * `刪除文章`
* 更新
  * `更新文章`
  * `更新編輯者`
  * `上傳文章圖片`

另外此篇測試也會用到 Mocha 的測試框架，讀者們可以先看以下介紹做初步的了解。

## Mocha 介紹
Mocha 是 Node.js 的其中一套測試框架，支援 BDD（Behavior Driven Development） TDD（Test Driven Development），
這邊我們會偏向使用 BDD 的方式來做，每個 test cast 都會有一個 `describe()`，來敘述測試的情境（scenario），
之後在把驗證結果放在 `it()` 裡。

詳細可參考：[mochajs - 官網](https://mochajs.org/)



## Spec 實作
接下來直接看程式碼比較好理解。

### 取得
  * `取得文章清單（index all post）`
  * `取得單一文章（find single post）`

檔案路徑：`server/controllers/post.spec.js`
```
describe('find one and all', (done) => {

    it("index all post", (done) => {

      request.get("/rest/post/") // 請求的 URI，使用 HTTP GET
      .expect(200) // 如果 HTTP Status code 成功回傳 200
      .end((error, res) => {
        let posts = res.body.posts; // 取出回傳值 posts

        posts.should.be.Array; // 驗證是否為陣列（Array）
        posts[0].id.should.greaterThan(0); // 驗證陣列第一項的Id要大於0

        done(error); // 回傳 mocha ，測試已經結束
      });

    });

    it("find single post", (done) => {

      request.get("/rest/post/1")
      .expect(200) // HTTP Status code 成功回傳 200
      .end((error, res) => {

        models.User.findById('1').then((result) =>{ //  從資料庫尋找 Id 是 1
          (result !== null).should.true // 驗正尋找到結果不是 null
          done(error);
        });
      });
    });

  });
```
##### 程式碼說明：
* 首先我們先定義了一個情境描述（describe） `'find one and all'` 裡頭包含了兩個具體描述（it） 分別為 `index all post`，`find single post`
* 在 `index all post` 裡先規劃好我們要取得所有文章的 URI 是 `request.get("/rest/post/")` 並且使用 HTTP GET 。
* `.expect(200)` 如果 HTTP Status code 成功回傳 200
* `let posts = res.body.posts` 取出回傳值 posts
* `posts.should.be.Array;` 驗證是否為陣列（Array）
* `posts[0].id.should.greaterThan(0);` 驗證陣列第一項的Id要大於0
* `done(error);` 回傳 mocha ，測試已經結束
* `models.User.findById('1')` 從資料庫尋找 Id 是 1
* `then((result) =>{(result !== null).should.true` 驗正尋找到結果不是 null



### 建立刪除
  * `建立文章`
  * `刪除文章`


```
describe('create and delete a post', () => {
    it('create', async (done) => {

      // 建立 Post 資料
      let seedPost = {
        title: 'createPostTitle',
        content: 'createPostContent',
        tags: ['tag1', 'tag2', 'tag3'],
        img: 'createPostImgDir'
      };

      // 送出新增 Post 請求
      let createResult = await new Promise((resolve, reject) => {
        request.post('/rest/post/') // 送出請求的 URI，使用 HTTP POST
        .send(seedPost) // 帶入 Post 資料
        .expect(200) // 如果 HTTP Status code 成功回傳 200
        .end((error, res) => {
          if (error) return reject(error);
          return resolve(res.body.post); // 回傳 post 值給 createResult
        });
      });

      let result = await createResult;
      console.log('spec returns result ', result);
      try {
        result.title.should.equal(seedPost.title); // 驗證回傳直的 result.title 是否等於為當初建立的值（seedPost.title）
        result.tags.should.eql(seedPost.tags);
        result.id.should.be.greaterThan(0);
        done();
      } catch (e) {
        done(e);
      }
    });
    it("delete", (done) => {

      let postId = 1; // 設定要刪除的 post Id 為 1

        request.delete("/rest/post/" + postId) // 送出請求的 URI，使用 HTTP DELETE
        .expect(200) // 如果 HTTP Status code 成功回傳 200
        .end((error, res) => {

          models.Post.findById(postId).then((result) =>{ //  從資料庫尋找 Id 是 1
            (result == null).should.true //  驗證沒有找到資料，因為已被刪除
            done(error);
          });

        });

      });
  });
```
##### 程式碼說明：
* `request.post('/rest/post/')` 設定請求 URI，並且使用 HTTP POST 發出請求
* `result.title.should.equal(seedPost.title)` 驗證回傳直的 result.title 是否等於為當初建立的值（seedPost.title）
* `request.delete("/rest/post/" + postId)` 設定請求 URI，並且使用 HTTP DELETE 發出請求

### 更新
  * `更新文章`（update post）
  * `更新編輯者`（update post editor）
  * `上傳文章圖片`（file Upload）

```
describe('update post and file Upload', (done) => {
    it("update post", (done) => {

      // 建立要更新的 post 資料
      let updatePost = {
        title: '111',
        content: 'ssss',
        tags: ['aa', 'bb']
      }

      request.put("/rest/post/1") // 送出請求的 URI，使用 HTTP PUT
      .expect(200)
      .send(updatePost) // 帶入更新的 Post 資料
      .end((error, res) => {

        //成功回傳 200 後，從資料庫取出 Id 是 1 的 post ,因為剛剛更新的目標 Post Id 是 1
        models.Post.find({
          where: {
            id: 1
          },
          include: [ { model: models.Tag } ] // 關聯出跟此 post 有關聯的 tag
        }).then((updatedPost)=>{
          updatedPost.Tags[0].name.should.be.equal('aa'); // 驗證 updatePost 的 Tag 欄位是否跟資料庫取出後的值一致
          updatedPost.Tags[1].name.should.be.equal('bb');
          done();

        });
      });
    });
    it("update post editor", (done) => {

      // 設定使用者為 Admin 權限
      sinon.stub(services.user, 'getAuthStatus', (app) =>{
        return {authority: 'admin'}; // 模擬回傳權限為 Admin
      });

      // 更新的資料
      let updateEditorId = {
        editorId: 2
      }

      request.put("/rest/post/updateEditor/1") // 送出請求的 URI，使用 HTTP PUT
      .expect(200)
      .send(updateEditorId) // 帶入更新的 Editor 資料
      .end((error, res) => {
        models.Post.find({
          where: {
            id: 1
          }
        }).then((updatedPost)=>{

          // 驗證更新資料是否與資料庫取出的資料相同
          updatedPost.EditorId.should.be.equal(updateEditorId.editorId);

          done();
        });
      });


    });
    it("file Upload", (done) => {
      request.post('/rest/post/fileUpload/') // 送出請求的 URI，使用 HTTP POST
      .attach('file', 'test/server/resources/mobious.png') // 附加圖片檔案
      .expect(200)
      .end(function(err, res) {
        done(err)
      });
    });
  });
```
##### 程式碼說明：
* `include: [ { model: models.Tag } ]`  關聯出跟此 post 有關聯的 tag
* `.attach('file', 'test/server/resources/mobious.png')`  附加圖片檔案

## 參考資源
* [sinonjs - 官網](http://sinonjs.org/)
* [mochajs - GitHub](https://github.com/mochajs/mocha)

## 下一步
依照功能需求寫 Spec 以後，接下來就是 [後端（Back-End）Contorller](Controller.md)  寫入每個方法要執行的程序邏輯 。
