# Controller 前言

首先先整理出我們會需要寫得方法有哪些：
* index — 取得所有文章
* get — 取得單一文章
* create — 建立文章
* update — 修改文章
* delete — 刪除文章
* updateEditor — 修改文章編輯者
* upload — 上傳圖片

另外此篇有使用到 ES6 Generator 以及 Sequelize 的新增修改刪除（CRUD）語法，因此在實作以前可以先閱讀以下介紹。

## Generator 介紹
// TODO： Generator 介紹未寫完

## Sequelize 新增修改刪除（CRUD）
這邊介紹此範例有使用到常見的 CRUD 方法有哪些
* [$tableName]：資料表名稱
* [$Model]：資料的物件
* `[$tableName].find({where: id: postId})`：使用 where 條件篩選資料
* `[$tableName].findById([$id])`：使用主鍵（Id）篩選
* `[$tableName].findAll()`：取的所有資料
* `[$tableName].create([$Model])`：新增資料
* `[$tableName].bulkCreate([$ModelArray])`：批次新增，bulkCreate 傳入參數為陣列
* `[$tableName].destroy({ where: { id: 1 }})`：使用 where 條件刪除資料
* `[$Model].save()`：更新資料
* `[$Model].destroy()`：刪除資料






# Contorller 實作
FilePath：server/controller/post.js
## index
```
exports.index = function *() {

  // 取得所有文章
  let posts = yield models.Post.findAll()

  // body 回傳 posts 物件
  this.body = {posts}
};
```
#### 程式碼說明：
* `yield models.Post.findAll()`：取得所有文章
* `this.body = {posts}`：body 回傳 posts 物件

## get
```
exports.get = function *() {

  // 取網址參數 id 的值
  let postId = this.params.id;


  let post = yield models.Post.find({

    // 條件篩選 id 是 postId 值
    where: {
      id: postId
    },

    // 關聯出跟此 post 有關的 tag 表，並且只顯示出 name 欄位
    include: [ { model: models.Tag ,attributes: ['name']} ]
  });

  this.body = {post}
};
```
#### 程式碼說明：
* `this.params.id;`：取網址參數 id 的值
* `where: { id: postId }`：條件篩選 id 是 postId 值
* `include: [ { model: models.Tag ,attributes: ['name']} ]`：關聯出跟此 post 有關的 tag 表，並且只顯示出 name 欄位
* `attributes: ['name']`：attributes 屬性可以針對只要顯示的欄位做設定

## create
```
exports.create = function *() {

  try {

    // 取得 request 的 body 值
    let tmpPost = this.request.body;

    // 從 Session 中取得此用者的 Id
    let UserId = yield services.user.getSessionUser(this)

    // 把 resquest body 值寫入要新建立的的物件中
    let post_data = {
      title: tmpPost.title,
      content: tmpPost.content,
      img: tmpPost.img,
      CreatorId: UserId.id,
      EditorId: UserId.id
    }

    let post = null;
    let tag_arr = [];
    let tmpTag = [];

    // 將 post 寫入資料庫
    let result = yield models.Post.create(post_data);

    // 取得剛剛寫入的 post.id
    let PostId = result.id;

    // request tag 存入陣列
    yield tmpPost.tags.map((tag) => {
      tmpTag.push({
        name: tag,
        PostId: PostId
        });
    });

    // 將 tag 批次寫入資料庫
    let tagResult = yield models.Tag.bulkCreate(tmpTag);

    tagResult.forEach((tr) => {
      tag_arr.push(tr.name);
    });


    post = result;
    post.setDataValue('tags', tag_arr);
    this.body = {post};

  } catch (error) {
  console.log(error.stack);
  this.body = {post, error};
  }
};
```
#### 程式碼說明：
* `this.request.body`：取得 request 的 body 值
* `services.user.getSessionUser(this)`：從 Session 中取得此用者的 Id
* `models.Post.create(post_data);`：將 post 寫入資料庫
* `models.Tag.bulkCreate(tmpTag);`：將 tag 批次寫入資料庫

## update
```
exports.update = function *() {

  try {
    let postId = this.params.id; // 取網址參數 id 的值
    let editPost = this.request.body; // 取得 request 的 body 值
    let UserId = services.user.getSessionUser(this).id; // 從 Session 中取得此用者的 Id
    let result = null;

    let post = yield models.Post.find({

      // 條件篩選 id 是 postId 值
      where: {
        id: postId
      },

      // 關聯出跟此 post 有關的 tag
      include: [ { model: models.Tag } ]
    });

    // 驗證使用者要是 Creator 或是 Editor 才能更改
    if( UserId === post.CreatorId || UserId === post.EditorId )
    {
      // 取出舊 post tag
      yield post.Tags.map((tag) => {
        let state = editPost.tags.indexOf(tag.name);

        // 比對舊的 tag 在新更新的 tag 中是否存在，如不存則在資料庫中刪除
        if(state === -1){
          // New Post Data not have this tag, Remove Tag.
          models.Tag.destroy({
            where:{
              id:tag.id
            }
          });

        }else {
          // Is exist remove in editTag
          // 如果已經存在資料庫則在要更新的 tag array 中刪除
          editPost.tags.splice(state,1);
        }
      });

      // 將 tag 寫入資料庫
      yield editPost.tags.map((tag) => {
        // Create new Tag
        models.Tag.create({
          name:tag,
          PostId:post.id
        });
      });

      // 將需要更新的欄位才存入
      post.title=editPost.title;
      post.content=editPost.content;
      post.img=editPost.img;

      // 將 post 更新至資料庫
      result = yield post.save();

      this.body = {result};
    }

  } catch (error) {
    console.log(error.stack);
    this.body = {result, error};
  }


};
```
#### 程式碼說明：
* `if( UserId === post.CreatorId || UserId === post.EditorId )`： 驗證使用者要是 Creator 或是 Editor 才能更改
* `models.Tag.destroy({ where: {id: tag.id }});`：從資料庫中刪除 tag
* `yield post.save();`：將 post 更新至資料庫

## delete
```
exports.delete = function *() {

  // 條件篩選 id 是 postId 值
  let postId = this.params.id;

  let result = null;

  try {
    let UserId = services.user.getSessionUser(this).id;
    let post = yield models.Post.findById(postId);

    // 驗證要是 Creator 或是 Editor 身份才能刪除
    if( UserId === post.CreatorId || UserId === post.EditorId )
      result = yield post.destroy() // 從資料庫中刪除此 post

    } catch (e) {
      console.error("delete post error", e);
    }

    this.body = {result}
  };
```
#### 程式碼說明：
* `post.destroy()`：從資料庫中刪除此 post

## updateEditor
```
exports.updateEditor = function *() {

    try {
      // 取的驗證狀態
      let authStatus = services.user.getAuthStatus(this);

      // 判斷權限是 admin
      if(authStatus.authority ==='admin'){
        let postId = this.params.id;
        let editorId = this.request.body.editorId;
        let result = null;

        // 前端的 Select 如果選擇預設則會是 0，Editor 則給 null 值
        if(editorId==='0'){
          editorId = null;
        };

        // 從資料庫取出此 post
        let post = yield models.Post.findById(postId);

        // 設定post 的 editor
        post.EditorId = editorId;

        // 寫入資料庫
        yield post.save();

        this.body = {post};
      }
    } catch (error) {
      console.log(error.stack);
      this.body = {result, error};
    }

  };

```
#### 程式碼說明：
* 這邊比較要注意的是，因為前端是用 DropDownList 還選擇 Editor 因此如果選是預設值 value 會是 0，
但是在 post 的欄位 Editor 是對應 User 的 Model ，因此給 0 會發生錯誤因為資料庫沒有此筆 User，所以這邊如果沒有指定 Editor 要給 null 值。

## upload
```
exports.upload = function* (next) {

  try {
    // multipart upload
    let parts = parse(this, {
      autoFields: true
    });
    let part;
    let dir = '.tmp/images/post/';

    // 檢查目錄是否存在，如不存在則建立一個
    fs.ensureDirSync(dir);

    // 隨機名稱
    let filename = Math.floor(Math.random()*1000000) + '.png';

    // 寫入上傳的檔案到本地端
    while (part = yield parts) {
      var stream = fs.createWriteStream(path.join(dir, filename));
      part.pipe(stream);
    }
    console.log('uploading %s -> %s', filename, stream.path);
    this.body = {success: true, filename: filename}

  } catch (e) {

    console.log(e.stack);
    this.body = {success: false};

  }
};
```
#### 程式碼說明：
* `fs.ensureDirSync(dir)` 檢查路徑是否存在，如不存在則建立。
* `Math.floor()` 回傳整數
* `Math.random()` 0 ~ 0.9999999(無窮小數)

## 參考資源
* [Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)
* [co-busboy](https://github.com/mscdex/busboy)

## 下一步
Contorller 完成以後，接下來後端剩下設定 [後端（Back-End）Route](Route.md) 後，外部就可以使用 URI 來對資料進行操作了。
