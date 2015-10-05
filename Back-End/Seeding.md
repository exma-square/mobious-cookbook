# Seeding
我們在每次重起 Server 時，會自動初始資料庫資料，目的是方便我們做測試，
因為以往可能要測試刪除資料的功能時，都要先建立資料才能進行刪除的測試，
其實這都是反覆的動作，如果可以避免將會省下不少時間。

## 設定初始資料

這邊比較要注意的是，裡面有用到 async / await，因為在某些資料像是帳號，權限兩個資料表是會有關連性在的。
舉來來說當我們要建立一個使用者，而每個使用者一定都會有權限，所以我們一定要先建立好使用者，才能建立對應到該使用者的權限，
因為在建立權限的時候會有外鍵要對應到使用者的 Id ，如果沒有先建立使用者取得使用者的 Id ，會沒辦法寫入到權限對應的外鍵使用者欄位。
因此我們要用 await 等待使用者先建立完成回傳使用者物件，之後在往下繼續執行程式碼。

檔案：`server/bootstrap.js`
```
'use strict';

export default async (cb) => {

  let picture = {
    "large":"http://api.randomuser.me/portraits/women/72.jpg",
    "medium":"http://api.randomuser.me/portraits/med/women/72.jpg",
    "thumbnail":"http://api.randomuser.me/portraits/thumb/women/72.jpg"
  }

  let visitorUser = {
    "username":"visitor",
    "password":"visitor",
    "gender":"male",
    "email":"visitor@visitor.com",
    "phone":"(951)-385-6121",
    "cell":"(657)-919-3511",
    "picture":JSON.stringify(picture),
    'activated':true
  }


  let editorUser = {
    "username":"editor",
    "password":"editor",
    "gender":"male",
    "email":"editor@editor.com",
    "phone":"(951)-385-6121",
    "cell":"(657)-919-3511",
    "picture":JSON.stringify(picture),
    'activated':true
  }

  let editorUser2 = {
    "username":"editor2",
    "password":"editor2",
    "gender":"male",
    "email":"editor2@editor.com",
    "phone":"(951)-385-6121",
    "cell":"(657)-919-3511",
    "picture":JSON.stringify(picture),
    'activated':true
  }

  let adminUser = {
    "username":"admin",
    "password":"admin",
    "gender":"male",
    "email":"admin@admin.com",
    "phone":"(951)-385-6121",
    "cell":"(657)-919-3511",
    "picture":JSON.stringify(picture),
    'activated':true
  }

  let adminUser2 = {
    "username":"admin2",
    "password":"admin2",
    "gender":"male",
    "email":"admin2@admin.com",
    "phone":"(951)-385-6121",
    "cell":"(657)-919-3511",
    "picture":JSON.stringify(picture),
    'activated':false
  }


  let newBeanBlack = {
    name: '黑珍珠',
    place: '哥斯大黎加 Costa Rica',
    breed: '卡杜拉 Caturra；卡杜艾 Catuai',
    manor: '查孔自家農場',
    method: '自然處理法;「黑珍珠」',
    roastDate: '2014年',
    roastType: '烘焙度',
    flavour: '風味',
    lat: 25.0339031,
    lon: 121.5645099,
    coverUrl: '',
    thumbCoverUrl: ''
  }

  let newBeanWhite = {
    name: '白珍珠',
    place: '哥斯大黎加 Costa Rica',
    breed: '卡杜拉 Caturra；卡杜艾 Catuai',
    manor: '查孔自家農場',
    method: '自然處理法;「黑珍珠」',
    roastDate: '2014年',
    roastType: '烘焙度',
    flavour: '風味',
    lat: 25.0339031,
    lon: 121.5645099,
    coverUrl: '',
    thumbCoverUrl: ''
  }

  let testPost1 = {
    title: '這是一篇文章',
    content: '西捨帥帥'
  }

  let testPost2 = {
    title: '這是兩篇文章',
    content: '蹤影帥帥'
  }

  let testPost3 = {
    title: '這是三篇文章',
    content: '三篇文章的內容'
  }

  let Comment1 = {
    author: '西捨',
    content: '我也這麼認為'
  }

  let Comment2 = {
    author: 'Rick',
    content: '感謝大大無私分享'
  }

  let Comment3 = {
    author: '是叉貓',
    content: '肥宅4ni'
  }

  let adminRole = {
    authority: 'admin'
  };

  let editorRole = {
    authority: 'editor'
  };

  let tag1 = {
    name: 'Ian shuai shuai de'
  };

  let tag2 = {
    name: 'Tz shuai shuai de'
  };

  let tag3 = {
    name: 'this is a tag'
  };


  try {

    // 新增使用者
    let createdVisitor = await models.User.create(visitorUser);
    let createdEditor = await models.User.create(editorUser);
    let createdEditor2 = await models.User.create(editorUser2);
    let createdAdmin = await models.User.create(adminUser);
    let createdAdmin2 = await models.User.create(adminUser2);

    // 新增權限
    let createdEditorRole = await models.Role.create(editorRole);
    let createdEditor2Role = await models.Role.create(editorRole);
    let createdAdminRole = await models.Role.create(adminRole);
    let createdAdminRole2 = await models.Role.create(adminRole);

    // 設定關聯使用者與權限
    await createdEditor.setRoles(createdEditorRole);
    await createdEditor2.setRoles(createdEditor2Role);
    await createdAdmin.setRoles(createdAdminRole);
    await createdAdmin2.setRoles(createdAdminRole2);

    await models.Bean.create(newBeanBlack);
    await models.Bean.create(newBeanWhite);

    // 新增文章
    let createdPost1 = await models.Post.create(testPost1);
    let createdPost2 = await models.Post.create(testPost2);
    let createdPost3 = await models.Post.create(testPost3);

    // 設定關聯文章與建立者
    await createdPost1.setCreator(createdEditor);

    // 新增文章標籤
    let createTag = await models.Tag.create(tag1);
    let createTag2 = await models.Tag.create(tag2);
    let createTag3 = await models.Tag.create(tag3);

    // 設定關聯文章與文章標籤
    await createdPost1.setTags([createTag,createTag2]);
    await createdPost2.setTags([createTag3]);

    // 新增留言
    let createdCom1 = await models.Comment.create(Comment1);
    let createdCom2 = await models.Comment.create(Comment2);
    let createdCom3 = await models.Comment.create(Comment3);


  } catch (e) {

    console.log("error", e);

  }
}
```

檔案：`server/koa.js`

啟動 server 時會執行初始資料庫資料。
```
import bootstrap from './bootstrap';

var liftApp = async () => {


  await models.sequelize.sync({force: config.connection.force})

  await bootstrap();
  app.listen(config.port);

  if (process.send) process.send('online');
  debug('koa')(`Application started on port ${config.port}`);

  return app;

}
```
## 下一步
[Mocha ─ API Testing](Mocha.html)
