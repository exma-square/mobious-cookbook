# Post Manager ─ Back-End

##### [後端（Back-End）範例包含](Back-End.md)：
* Model（sequelize） — 定義資料表型別及設定關聯性
* Spec — 定義規格及測試
* Route — RESTful API 路由設定
* Controller — 處理邏輯層


## Model
首先依照我們的需求總共要定義四張資料表：
* `User` — 使用者帳號資料
  + username：使用者名稱（帳號）
  + gender：性別
  + email：電子郵件
  + password：密碼
  + phone：電話
  + cell：手機
  + picture：大頭貼
  + activiated：啟用狀態
  + Role（hasMany）：權限（多）
  + Post（hasMany）：文章（多）
* `Role` — 權限
  + authority：權限
  + User（FK）：使用者
* `Post` — 文章
  + content：文章內容
  + title：文章名稱
  + img：圖片
  + creater（FK）：建立者（User）
  + editor（FK）：編輯者（User）
  + Tag（FK）：標籤（Tag）
* `Tag`— 文章標籤

我們定義文章的 Model

FilePath：`server/models/user.js`
```
'use strict';

module.exports = (sequelize, DataTypes) => {
  var User = sequelize.define('User', {
    username: DataTypes.STRING,
    gender: DataTypes.STRING,
    email: DataTypes.STRING,
    password: DataTypes.STRING,
    phone: DataTypes.STRING,
    cell: DataTypes.STRING,
    picture: DataTypes.TEXT,
    activated: DataTypes.BOOLEAN
  }, {
    classMethods: {
      associate: (models) => {
        User.hasMany(models.Post);
        User.hasMany(models.Role);
      }
    }
  });

  return User;
};
```

FilePath：`server/models/role.js`
```
module.exports = (sequelize, DataTypes) => {
  var Role = sequelize.define('Role', {
    authority: DataTypes.STRING
  }, {
    classMethods: {
      associate: (models) => {
        Role.belongsTo(models.User);
      }
    }
  });

  return Role;
};
```


FilePath：`server/models/post.js`
```
'use strict';
module.exports = (sequelize, DataTypes) => {
  var Post = sequelize.define('Post', {
    title: DataTypes.STRING,
    content: DataTypes.STRING,
    img: DataTypes.STRING
  }, {
    classMethods: {
      associate: (models) => {
        Post.belongsTo(models.User, {as: 'Creator'} ),
        Post.belongsTo(models.User, {as: 'Editor'} ),
        Post.hasMany(models.Tag);
      }
    },
    paranoid: true
  });

  return Post;
};
```
程式碼說明：
* `DataTypes.STRING` 定義欄位為字串類型
* ```Post.belongsTo(models.User, {as: 'Creator'}```
設定外鍵指向 ```User```資料表並設定別名為Creator

```
'use strict';

module.exports = (sequelize, DataTypes) => {
  var User = sequelize.define('User', {
    username: DataTypes.STRING,
    gender: DataTypes.STRING,
    email: DataTypes.STRING,
    password: DataTypes.STRING,
    phone: DataTypes.STRING,
    cell: DataTypes.STRING,
    picture: DataTypes.TEXT,
    activated: DataTypes.BOOLEAN
  }, {
    classMethods: {
      associate: (models) => {
        User.hasMany(models.Post);
        User.hasMany(models.Role);
      }
    }
  });

  return User;
};
```


```
module.exports = (sequelize, DataTypes) => {
  var Tag = sequelize.define('Tag', {
    name: DataTypes.STRING
  }, {
    classMethods: {
      associate: (models) => {
          Tag.belongsTo(models.Post);
      }
    }
  });

  return Tag;
};
```


## Spec
`取得文章清單`
```

  describe('find one and all', (done) => {


    it("index all post", (done) => {

      request.get("/rest/post/")
      .expect(200)
      .end((error, res) => {
        let posts = res.body.posts;

        posts.should.be.Array;
        posts[0].id.should.greaterThan(0);

        done(error);
      });

    });

    it("find single post", (done) => {

      request.get("/rest/post/1")
      .expect(200)
      .end((error, res) => {

        models.User.findById('1').then((result) =>{
          (result !== null).should.true
          done(error);
        });
      });
    });
  });

```




## Route

## Controller
