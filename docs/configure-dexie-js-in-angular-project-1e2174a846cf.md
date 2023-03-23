# 在角度项目中配置 Dexie.js

> 原文：<https://betterprogramming.pub/configure-dexie-js-in-angular-project-1e2174a846cf>

## js 是 IndexedDB 的极简包装器

![](img/caa17934641b71be4afc4e5aededa5f1.png)

照片由[哈尔·盖特伍德](https://unsplash.com/@halacious?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 索引 b

IndexedDB 是最流行的浏览器内(客户端)数据库。它以键值格式存储数据。它极大地简化了数据库版本控制任务。这里，表/关系通常被称为*对象存储*(或简称为*存储*)。我们可以为对象存储创建索引来加速查询。

它使用客户端机器上的存储来存储其数据库，因此其存储限制取决于通常具有高容量的客户端机器。

IndexedDB API 是可用的，通常由 Javascript 访问。然而，为了扩大它的范围，简化过程和增加特性，可以使用几个包装器和节点包。

# Dexie.js

Dexie.js 是 IndexedDB 的极简包装器。使用 IndexedDB 是一种简单的方法，可以轻松集成多个前端框架。

Dexie.js API 减少了样板代码，并在查询格式方面提供了更大的变化。这是一个有良好文档记录的 API，可以作为节点包安装。

## 安装 Dexie.js

1.  打开命令提示符。
2.  移动到 angular 项目父目录。
3.  运行以下命令通过 npm 安装 dexie.js

```
npm i dexie
```

## 在 Angular 中为 Dexie.js 创建服务

1.  打开命令提示符。
2.  移动到 angular 项目父目录。
3.  运行以下命令创建一个名为`db`的服务:

```
ng generate service db
```

或者

```
ng g s db
```

将在 angular 项目的`src/app`文件夹中创建两个新文件。

*   `db.service.spec.ts`
*   `db.service.ts`

db.service.ts 将进一步包含配置、数据库、存储(表)、键等的代码。

## 角度服务中的 Dexie.js 配置

1.  `db.service.ts`进口德协

```
import Dexie from 'dexie';
```

2.创建一个名为`DbService`的类，并继承`Dexie`类:

```
export class DbService extends Dexie {....}
```

3.创建一个构造函数(使用`constructor`方法)并调用`super`方法，将数据库的名称作为参数(这里是`DexieDB`)。构造函数是类的对象一创建就执行的函数。它用于初始化类的成员变量，并有一些扩展的用法。`super`方法用于调用继承类的构造函数(这里是`Dexie`)。传递给超方法的参数最终会传递给继承类的构造函数。

```
constructor() {
    super("DexieDB");
    ....
    ....
    ....
}
```

4.使用 Dexie 的`version`方法定义数据库的版本(这里版本号设为 1)并列出所有的存储(表)。虽然 IndexedDB 是无模式的，但是 Dexie.js 要求我们预先定义所有字段(`columns`)作为主键(或者简单地称为`key`或`indices`)(我们可能会在以后的查询、搜索、排序中使用)。

```
this.version(1).stores({
    myStore1: '++empId, empName, empSal',
    myStore2: 'compId, compName'
});
```

这里，创建了两个名为`myStore1`和`myStore2`的商店(表)。在`myStore1`中，`empId`为主键， ***++*** 定义为从整数 *1* 开始自动递增。`empName`和`empSal`是`myStore1`的字段，稍后将在应用程序中用于查询、搜索、排序(索引/多个索引)。在`myStore2`中，`compId`是主键，`compName`字段稍后将用于查询、搜索和排序(索引/多个索引)。

5.数据库已创建，但仍需要打开才能进行操作。为此使用 Dexie 的`open`方法。它返回一个承诺，该承诺可用于不同的目的，如填充数据库、用数据库中的记录初始化应用程序等。

```
this.open()
.then(data => console.log("DB Opened"))
.catch(err => console.log(err.message));
```

如果数据库成功打开，将执行`then`块。如果出现任何错误，将执行`catch`程序块。

最终的代码看起来会像这样-

db . service . ts(Angular 项目中的 Dexie.js 配置)

## 访问 Angular 项目组件中的 Dexie.js 存储(表)

1.  在*中。组件的 ts* 文件，只需导入在`db.service.ts`中创建的类，即`DbService`类:

```
import { DbService } from 'db.service';
```

2.在组件类的构造函数中创建一个`DbService`类的对象。

```
....
....
    constructor(private db: DbService) {}
....
....
```

所有的`dexie.js` API 方法(添加、获取、上传、更新、删除、where 等)都可以被这个对象访问。

3.运行以下代码，在`myStore1`商店(表)中添加一条新记录:

```
addRecord(): void {
    this.db.table('myStore1')
    .add({empName: 'Mohit Kushwaha', empSal: 1000})
    .then(data => console.log(data))
    .catch(err => console.log(err.message));
}
```

因为主键`empId`是自动递增的，所以我们不需要提供它。它会自动生成。以类似的方式，可以执行所有其他操作。

要了解更多关于 IndexedDB 的信息，请访问网站[这里](https://www.w3.org/TR/IndexedDB/)。要了解更多关于`dexie.js`的信息，请访问官方网站[这里](https://dexie.org/)。