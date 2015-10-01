# Model 前言
我們使用的是 [Sequelize](#Sequelize) 來設定資料表的關聯及型別，因此在建立資料表之前可以先瀏覽 [Model ─ Sequelize](../../Back-End/Model.md) 此章節做個初步的認識。

server
* config
  * ∟ development.js — 資料庫連線設定檔（開發環境）
  * ∟ production.js — 資料庫連線設定檔（生產環境）
  * ∟ init.js — server 連線資訊設定檔（domain、url、port）
* models
  * ∟ user.js — 使用者資料表
  * ∟ role.js — 權限資料表
  * ∟ post.js — 文章資料表
  * ∟ tag.js — 文章標籤資料表


## Model 實作
依照我們 Post Manager 的需求總共會需要四張資料表：[User](#User)，[Role](#Role)，[Post](#Post)，[Tag](#Tag)。

## User{#User}
檔案路徑：`server/models/user.js`
* `User` — 使用者帳號資料表
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
##### 程式碼說明：
* `username: DataTypes.STRING`：username 為 STRING 型別
* `picture: DataTypes.TEXT,`：picture 為 TEXT 型別
* `activated: DataTypes.BOOLEAN`：activated 為 BOOLEAN 型別
* `User.hasMany(models.Post)`：User 可以擁有很多個Post（一對多）
* `User.hasMany(models.Role)`：User 可以擁有很多個Role（一對多）

## Role{#Role}
檔案路徑：`server/models/role.js`
* `Role` — 權限表
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
##### 程式碼說明：
* `Role.belongsTo(models.User)`：Role 是屬於 User 的，也就是說 Role 一定會有一個外鍵 User 值是 User.Id

## Rost{#Post}
檔案路徑：`server/models/post.js`
* `Post` — 文章表
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
##### 程式碼說明：
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
##### 程式碼說明：
* `Tag.belongsTo(models.Post)` ：每個 Tag 都一定會對應到一個Post

## 資料庫連線設定
在 config 資料夾底下有兩個 development.js 及 production.js ，分別對應開發環境及生產環境的資料庫連線資訊，
在開發環境中我們是使用 sqlite 來做本機資料庫。

檔案路徑：`server/config/development.js`
```
export default {
  port: parseInt(process.env.PORT, 10) || 3000,
  'connection': {
    'dialect': 'sqlite',
    'storage': './db.development.sqlite',
    'username': null,
    'password': null,
    'database': null,
    'force': true
  }
};
```

## 參考資源
* [Sequelize DataTypes](http://docs.sequelizejs.com/en/latest/api/datatypes/)

## 下一步
定義完 Model 以後，接下來就是寫 [後端（Back-End）Spec](Spec.html)  確認我們的規格及測試的撰寫 。
