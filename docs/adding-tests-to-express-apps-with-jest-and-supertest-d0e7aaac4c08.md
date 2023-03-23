# 使用 Jest 和 SuperTest 向 Express 应用程序添加测试

> 原文：<https://betterprogramming.pub/adding-tests-to-express-apps-with-jest-and-supertest-d0e7aaac4c08>

## 通过可靠的测试防止应用程序故障

![](img/fe478be2e4e3289708a17b8eecfac826.png)

[钟](https://unsplash.com/@zlg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

自动化测试对我们编写的应用程序至关重要，因为现代应用程序有如此多的活动部件。

在本文中，我们将看看如何编写应用程序来测试一个 Express 应用程序，该应用程序使用 [Jest](https://jestjs.io/) 和 [SuperTest](https://github.com/visionmedia/supertest) 与数据库进行交互。

# 创建我们将测试的应用程序

我们通过创建一个空文件夹来创建一个项目文件夹，并运行以下命令来创建一个带有默认答案的`package.json`文件:

```
npm init -y
```

然后，我们运行以下程序来为我们的应用程序安装软件包:

```
npm i express sqlite3 body-parser
```

然后，我们为我们的应用程序创建`app.js`文件，并编写:

```
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const bodyParser = require('body-parser');
const app = express();
const port = process.env.NODE_ENV === 'test' ? 3001 : 3000;
let db;
if (process.env.NODE_ENV === 'test') {
    db = new sqlite3.Database(':memory:');
}
else {
    db = new sqlite3.Database('db.sqlite');
}db.serialize(() => {
    db.run('CREATE TABLE IF NOT EXISTS persons (id INTEGER PRIMARY KEY, name TEXT, age INTEGER)');
});app.use(bodyParser.json());
app.get('/', (req, res) => {
    db.serialize(() => {
        db.all('SELECT * FROM persons', [], (err, rows) => {
            res.json(rows);
        });
    })
})app.post('/', (req, res) => {
    const { name, age } = req.body;
    db.serialize(() => {
        const stmt = db.prepare('INSERT INTO persons (name, age) VALUES (?, ?)');
        stmt.run(name, age);
        stmt.finalize();
        res.json(req.body);
    })
})app.put('/:id', (req, res) => {
    const { name, age } = req.body;
    const { id } = req.params;
    db.serialize(() => {
        const stmt = db.prepare('UPDATE persons SET name = ?, age = ? WHERE id = ?');
        stmt.run(name, age, id);
        stmt.finalize();
        res.json(req.body);
    })
})app.delete('/:id', (req, res) => {
    const { id } = req.params;
    db.serialize(() => {
        const stmt = db.prepare('DELETE FROM persons WHERE id = ?');
        stmt.run(id);
        stmt.finalize();
        res.json(req.body);
    })
})const server = app.listen(port);
module.exports = { app, server };
```

上面的代码包含了我们将要测试的应用程序。

为了让我们的应用更容易测试，我们有:

```
const port = process.env.NODE_ENV === 'test' ? 3001 : 3000;
let db;
if (process.env.NODE_ENV === 'test') {
    db = new sqlite3.Database(':memory:');
}
else {
    db = new sqlite3.Database('db.sqlite');
}
```

因此，我们可以将`process.env.NODE_ENV`设置为`'test'`，让我们的应用程序监听一个与它在非测试环境中运行时不同的端口。

我们将使用`'test'`环境来运行我们的测试。

同样，我们希望我们的应用程序在运行单元测试时使用与不运行时不同的数据库。

这就是为什么我们有:

```
let db;
if (process.env.NODE_ENV === 'test') {
    db = new sqlite3.Database(':memory:');
}
else {
    db = new sqlite3.Database('db.sqlite');
}
```

我们指定当应用程序在`'test'`环境中运行时，我们希望使用 SQLite 的内存数据库，而不是数据库文件。

# 编写测试

## 初始化代码

有了可测试的应用程序，我们就可以向它添加测试。

我们将使用 Jest test runner 和 SuperTest 在测试中向我们的路线发出请求。为了添加 Jest 和 SuperTest，我们运行:

```
npm i jest supertest
```

然后，我们将`app.test.js`添加到与上面的`app.js`文件相同的文件夹中。

在`app.test.js`中，我们开始编写以下内容:

```
const { app } = require('./app');
const sqlite3 = require('sqlite3').verbose();
const request = require('supertest');
const db = new sqlite3.Database(':memory:');beforeAll(() => {
    process.env.NODE_ENV = 'test';
})
```

在上面的代码中，我们包含了来自`app.js`的 Express 应用程序。然后我们还包括了 SQLite3 和 SuperTest 包。,

然后，我们通过以下方式连接到内存数据库:

```
const db = new sqlite3.Database(':memory:');
```

接下来，我们通过运行以下命令将所有测试设置为在`'test'`环境中运行:

```
beforeAll(() => {
    process.env.NODE_ENV = 'test';
})
```

这将确保我们为每个测试使用端口`3001`和我们在`app.js`中指定的内存数据库。

为了使我们的测试独立运行并得到一致的结果，我们必须每次都清理数据库并插入新的数据。

为此，我们创建了一个在每次测试中调用的函数:

```
const seedDb = db => {
    db.run('CREATE TABLE IF NOT EXISTS persons (id INTEGER PRIMARY KEY, name TEXT, age INTEGER)');
    db.run('DELETE FROM persons');
    const stmt = db.prepare('INSERT INTO persons (name, age) VALUES (?, ?)');
    stmt.run('Jane', 1);
    stmt.finalize();
}
```

上面的代码创建了一个`persons`表(如果它不存在的话),然后删除其中的所有内容。

然后，我们在那里插入一个新值，以获得一些起始数据。

# 添加测试

初始化代码完成后，我们就可以编写测试了。

## **获取请求测试**

首先，我们编写一个测试，用一个`GET`请求从数据库中获取现有的种子数据。

我们通过编写以下内容来做到这一点:

```
test('get persons', () => {
    db.serialize(async () => {
        seedDb(db);
        const res = await request(app).get('/');
        const response = [
            { name: 'Jane', id: 1, age: 1 }
        ]
        expect(res.status).toBe(200);
        expect(res.body).toEqual(response);
    })
});
```

我们把所有东西都放在了`db.serialize`的回调函数中，这样查询将会按顺序运行。

首先，我们调用上面创建的`seedDb`，如果表不存在就创建它，清空数据库，并添加新数据。

然后，我们写信给`GET`请求:

```
await request(app).get('/');
```

这使我们得到了从承诺解析出响应的`res`对象。

`request(app)`将启动 Express 应用程序，以便我们提出请求。

接下来，我们要检查`response`的正确性:

```
const response = [
  { name: 'Jane', id: 1, age: 1 }
]
```

然后，我们检查回答，看看我们是否得到了我们期望的结果:

```
expect(res.status).toBe(200);
expect(res.body).toEqual(response);
```

`toBe`方法检查浅相等，而`toEqual`检查深相等。所以我们用`toEqual`来检查整个对象结构是否相同。

`res.status`检查服务器返回的状态码，`res.body`有响应体。

## **岗位要求测试**

接下来，我们为`POST`请求添加一个测试。这类似于`GET`请求测试。

我们编写以下代码:

```
test('add person', () => {
    db.serialize(async () => {
        seedDb(db);
        await request(app)
            .post('/')
            .send({ name: 'Joe', age: 2 }); const res = await request(app).get('/');
        const response = [
            { name: 'Jane', id: 1, age: 1 },
            { name: 'Joe', id: 2, age: 2 }
        ]
        expect(res.status).toBe(200);
        expect(res.body).toEqual(response);
    })
});
```

首先，我们用以下内容重置数据库:

```
seedDb(db);
```

我们向以下人员提出了我们的`POST`请求:

```
await request(app)
  .post('/')
  .send({ name: 'Joe', age: 2 });
```

这将在内存数据库中插入一个新条目。

最后，为了检查正确性，我们发出`GET`请求——就像我们的第一个测试一样——并检查是否返回了两个条目:

```
const res = await request(app).get('/');
const response = [
  { name: 'Jane', id: 1, age: 1 },
  { name: 'Joe', id: 2, age: 2 }
]
expect(res.status).toBe(200);
expect(res.body).toEqual(response);
```

## **上传和删除测试**

`PUT`请求的测试类似于`POST`请求。我们重置数据库，使用我们的有效负载发出`PUT`请求，然后发出`GET`请求以获取返回的数据，如下所示:

```
test('update person', () => {
    db.serialize(async () => {
        seedDb(db);
        await request(app)
            .put('/1')
            .send({ name: 'Joe', age: 2 }); const res = await request(app).get('/');
        const response = [
            { name: 'Jane', id: 1, age: 1 }
        ]
        expect(res.status).toBe(200);
        expect(res.body).toEqual(response);
    })
});
```

然后我们可以用`DELETE`请求替换`PUT`请求，并测试`DELETE`请求:

```
test('delete person', () => {
    db.serialize(async () => {
        seedDb(db);
        const res = await request(app).delete('/1');
        const response = [];
        expect(res.status).toBe(200);
        expect(res.body).toEqual(response);
    })
});
```

# 运行测试

为了运行测试，我们将以下内容添加到`scripts`部分:

```
"test": "jest --forceExit"
```

我们必须添加`--forceExit`选项，这样 Jest 将在测试运行后存在。使用 SuperTest 的 Jest 测试不能正确退出的问题还没有解决。

然后，我们运行以下程序来运行测试:

```
npm test
```

我们应该得到:

```
PASS  ./app.test.js
  √ get persons (11ms)
  √ add person (2ms)
  √ update person (2ms)
  √ delete person (6ms)Test Suites: 1 passed, 1 total
Tests:       4 passed, 4 total
Snapshots:   0 total
Time:        2.559s
Ran all test suites.
Force exiting Jest: Have you considered using `--detectOpenHandles` to detect async operations that kept running after all tests finished?
```

无论我们运行多少次测试，我们都应该得到相同的结果，因为我们重置了数据库，并使所有数据库查询按顺序运行。

此外，我们在测试中使用了与其他环境不同的数据库和端口，所以数据应该是干净的。

# 结论

我们可以用 Jest 测试运行器添加测试运行。为此，我们必须有不同的端口和数据库来运行测试。然后，如果这些表不存在，我们就创建它们，清除所有数据，并添加种子数据，这样我们就可以为每个测试提供相同的数据库结构和内容。

通过 SuperTest，我们可以自动运行 Express 应用程序，并提出我们想要的请求。然后，我们可以检查输出。