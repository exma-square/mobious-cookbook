# Model
首先依照我們 Post Manager 的需求總共會需要四張資料表：[User](#User)，[Role](#Role)，[Post](#Post)，[Tag](#Tag)

## User{#User}
FilePath：`server/models/user.js`
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
#### 程式碼說明：
* `username: DataTypes.STRING`：username 為 STRING 型別
* `picture: DataTypes.TEXT,`：picture 為 TEXT 型別
* `activated: DataTypes.BOOLEAN`：activated 為 BOOLEAN 型別
* `User.hasMany(models.Post)`：User 可以擁有很多個Post（一對多）
* `User.hasMany(models.Role)`：User 可以擁有很多個Role（一對多）

## Role{#Role}
FilePath：`server/models/role.js`
* `Role` — 權限
  + authority：權限
  + User（FK）：使用者

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
#### 程式碼說明：
* `Role.belongsTo(models.User)`：Role 是屬於 User 的，也就是說 Role 一定會有一個外鍵 User 值是 User.Id

## Rost{#Post}
FilePath：`server/models/post.js`
* `Post` — 文章
  + content：文章內容
  + title：文章名稱
  + img：圖片
  + creater（FK）：建立者（User）
  + editor（FK）：編輯者（User）
  + Tag（FK）：標籤（Tag）

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
#### 程式碼說明：
* `Post.belongsTo(models.User, {as: 'Creator'}` ：設定外鍵指向 ```User```資料表並設定別名為Creator
* `Post.belongsTo(models.User, {as: 'Editor'}` ：設定外鍵指向 ```User```資料表並設定別名為Editor
* `Post.hasMany(models.Tag)` ：Post 可以有很多個Tag
> 注意：如果有兩個外鍵都指向同一個 table 表，就要給予別名（as屬性）,以此範例來說有兩個 User 就要分別給別名，
否則最後產生時只會只有一個欄位出現.

## Tag{#Tag}
* `Tag`— 文章標籤
FilePath：`server/models/tag.js`

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
#### 程式碼說明：
* `Tag.belongsTo(models.Post)` ：每個 Tag 都一定會對應到一個Post

## 參考資源
* [Sequelize DataTypes](http://docs.sequelizejs.com/en/latest/api/datatypes/)

## 下一步
定義完 Model 以後，接下來就是寫 [後端（Back-End）Spec](Spec.md)  確認我們的規格及測試的撰寫。
