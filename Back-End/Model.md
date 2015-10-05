# Model ─ Sequelize

[Sequelize](http://sequelize.readthedocs.org/en/latest/) 是 Node.js 、 io.js 的一種 ORM（Object Relational Mapping），ORM 中文稱作「物件關係對映」
意思是可以把物件轉換成不同資料庫語言的方法，舉例來說如果使用 MySQL 就要會 MySQL 的資料庫語法，換成 MSSQL
就又要學 MSQQL 資料庫語法，會造成每種版本資料庫都會有不同的寫法，因此 ORM 的好處就是可以只需一種寫法，可以自動幫你轉換成不同資料庫版本的語法，
且也可避免網頁工程師直接對資料庫進行操作，降低了物件導向與資料庫之間的耦合關係。

[Sequelize](http://sequelize.readthedocs.org/en/latest/) 目前支援 PostgreSQL、MySQL、MariaDB、SQLite、MSSQL 。


##安裝
`npm install sequelize --save`

##使用
* 引用 `sequelize`

建立了一個 User 資料表，內有兩個欄位 username ，birthday 資料型態分別是字串與時間：
```
var Sequelize = require('sequelize');
var sequelize = new Sequelize('database', 'username', 'password');

var User = sequelize.define('User', {
  firstName: Sequelize.STRING,
  lastname: Sequelize.STRING,
  status:  Sequelize.BOOLEAN
});

```
常用的資料型態：
* Sequelize.STRING — 字串（可變動的長度字串）
* Sequelize.CHAR — 字串（固定的長度字串）
* Sequelize.TEXT —  字串（用來儲存大量的字元資料）
* Sequelize.INTEGER — 數字
* Sequelize.FLOAT — 浮點數
* Sequelize.BOOLEAN — 布林值

...等等，資料庫型別還有非常多種，其他可參考 [Sequelize DataTypes](http://docs.sequelizejs.com/en/latest/api/datatypes/) 。


建立資料表，新增資料：
> force 參數：如果 table 存在則刪除（Drop） 並建立新的 table。

```
User.sync({force: true}).then(function () {
  User.create({
    firstName: 'Caesar',
    lastName: 'Chi',
    status: false
    });
});
```

檢索資料（find）：
```
User.findById(id);
User.findAll();
```

刪除資料（destroy）：
```
User.destroy({ where: { id: 1 }}); // 使用條件篩選
User.destroy(user); // 直接帶入 user 物件
```

修改資料（update）：
```
var user = User.findById(id);
user.status = true;
user.save();

// 或是

User.update({
  status: true,
}, {
  where: {id: 12}  // 要修改資料的 id
});
```


條件篩選（where）：
```
User.findAll({
  where: {
    id: 2
  }
});
// SELECT * FROM user WHERE id = 2

User.findAll({
  where: {
    id: 12,
    status: true
  }
});
// SELECT * FROM user WHERE id = 12 AND status = 'active';

User.destroy({
  where: {
    status: true
  }
});
// DELETE FROM user WHERE status = true;

User.update({
  status: false,
}, {
  where: {
    id: 12
  }
});
// UPDATE user SET status = false WHERE id = 12;
```

##參考資料
* [Sequelize 官網](http://sequelize.readthedocs.org/en/latest/)
* [Sequelize DataTypes](http://docs.sequelizejs.com/en/latest/api/datatypes/)

## 下一步
[Controller](Controller.html)
