# 使用 PostgreSQL 和 Sequelize 从多个表中获取数据

> 原文：<https://betterprogramming.pub/fetch-the-data-from-multiple-tables-using-postgresql-and-sequelize-dc9ea69a34>

## 从 postgreSQL 中的多个模型获取记录

![](img/3aaa7d32d04ff96c58059d99d43e9f8b.png)

来源:google.com

我正在开发一个基于 Angular 和 Node.js 的财务应用程序，我使用的数据库是 PostgreSQL 和 [Sequelize](https://sequelize.org/) 。

在工作期间，我觉得 PostgreSQL 和 Sequelize 从多个模型中获取数据有点复杂，所以我想我应该写这篇文章。

# 让我们开始吧

在本文中，我们将使用两个模型(表),并尝试使用一个查询从两个模型中获取数据——就像 MongoDB 中的`populate`和 SQL 中的`join`。但是在这里，在 PostgresSQL 中，不像在 MongoDB 中那么简单。我们需要在模型文件中添加一些东西。

在这里，我创建了两个模型:`UserModel`和`OrderModel`。让我们来看看每个模型文件:

## **UserModel.js**

```
'use strict';
module.exports = function (Sequelize, DataTypes) {
 const **Users** = Sequelize.define('Users', {
  id: {
    allowNull: false,
    type: DataTypes.INTEGER,
    autoIncrement: true,
    primaryKey: true
  }, fName: {
   allowNull: false,
   type: DataTypes.STRING
 }, lName: {
   allowNull: false,
   type: DataTypes.STRING
 }, orders:{
   allowNull: true,
   type:DataTypes.INTEGER
 }
 }, {
  timestamps: true,
  classMethods: {
  associate: function (models) {
        Users.belongsTo(models.Orders, { foreignKey : ‘orders’ });
  }
 }
});return **Users**;
};
```

我们需要定义一个`associate`函数，其中我们需要定义我们的关联模型(这里我们有`Users`和`Orders`模型)，我们需要提供`foreignKey`。

让我们看看我们的`OrderModel.js`文件。

## **OrderModel.js**

```
'use strict';
module.exports = function (Sequelize, DataTypes) {
 const **Orders** = Sequelize.define('Orders', {
  id: {
   allowNull: false,
   type: DataTypes.INTEGER,
   autoIncrement: true,
   primaryKey: true
  },
  user: {
   allowNull: false,
   type: DataTypes.INTEGER
  }, category: {
   allowNull: false,
   type: DataTypes.STRING
   },
  price: {
   allowNull: false,
   type: DataTypes.INTEGER
  }
 }, {
   timestamps: true,
   classMethods: {
    associate: function (models) {
     Orders.hasMany(models.Users, { as: ‘orders’, foreignKey : ‘orders’ });
    }
   }
  });
 return **Orders**;
};
```

`Users`型号有以下记录:

```
id  fName     lName    orders1   Shubham   Verma     1
2   Anish     Chand     2
```

`Orders`型号有以下记录 **:**

```
Id   userId    price     category1    1         1000      ONLINE
2    2         2000      CASH
```

现在，我想从上述两个模型中获取组合记录，如下所示:

```
[{
"id": "1",
"fName": "Shubham",
"lName": "Verma",
"orders":{
            "id": 1,
            "userId": 1,
            "price": 1000,
            "category": "ONLINE"
         }
 },
 {
"id": 2,
"fName": "Anish",
"lName": "Chand",
"orders": {
            "id": 2,
            "userId": 2,
            "price": 2000,
            "category": "CASH"
          }
 }]
```

我们在两个模型中都有所有的模型和数据。现在，我们需要进行查询来获得合并的结果。

所以我们的查询应该是这样的:

```
 Models.Users.findAll({
  where: {},
  include: [{
      model: Models.Orders,
      where: {}
  }]
}).then((data) => {

      console.log("*********************\n\n\n\n\n",data);});
```

在上面的查询中，我们包含了带有`where`条件的模型`Orders` ( `Model.Orders`)。您可以添加一个条件来从`Orders`模型中获取特定的记录。

根据上面的查询，我们会得到以下结果:

```
[{
"id": "1",
"fName": "Shubham",
"lName": "Verma",
"orders":{
            "id": 1,
            "userId": 1,
            "price": 1000,
            "category": "ONLINE"
         }
 },
 {
"id": 2,
"fName": "Anish",
"lName": "Chand",
"orders": {
            "id": 2,
            "userId": 2,
            "price": 2000,
            "category": "CASH"
          }
 }]
```

在这里，我们达到了预期的结果。

# 结论:

在本文中，我们学习了以下内容:
*如何使用 PostgreSQL 和 Sequelize 从多个表中获取数据？
*如何使用 PostgreSQL 和 Sequelize 创建模型？
*如何用 PostgreSQL 和 Sequelize 编写查询？

感谢阅读。如果你对 Node.js 或者 JavaScript 感兴趣，那么[这个链接](https://medium.com/@svsh227/every-nodejs-developer-should-read-these-articles-f29032441a31)会有帮助。