# iOS 上的 SQLite:MVVM 之路

> 原文：<https://betterprogramming.pub/sqlite-on-ios-the-mvvm-way-8a4827d62519>

## SwiftUI 遇上 SQLite

![](img/c15bd85f7e1e9bad555d204c07c1e4e1.png)

照片由[迈克尔·泽兹奇](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

上周，我在研究 NetNewsWire ,看看我是否能在我的项目中找到一些有趣的东西。

我发现的一件很酷的事情是，这个项目没有使用 CoreData，而是使用了 SQLite。该项目的作者在 Sundell podcast 第 95 集上解释了为什么采用 SQLite 极大地提高了性能的一些优点。

有时，您可能想从数据存储中获得更多，您想要更多的控制，或者您只想使用表格和 SQL lite 语句。

我想尝试一下，因为我与 CoreData 和 CloudKit 集成的关系不太好，而且我不是 GUI 的粉丝。还因为我在后端服务上做了很多工作，所以我更喜欢在数据层工作，自己优化索引和查询，并把它们写下来。

如果你想看一看一个要点中的最终代码，你可以在这里找到[并跟随。](https://gist.github.com/mattrighetti/4db0c5266fe8717509ab9048895461ba)

# FMDB 简介

[FMDB](https://github.com/ccgus/fmdb[FMDB) 是一个围绕 SQLite 的 Objective-C 包装器，它是开源的，非常容易设置，而且它恰好是唯一一个允许您这样做的好库(在这里我可能是错的，如果您知道其他好的库，请告诉我，我很乐意尝试一下！)

# 设置

让我们创建一个新的 Xcode 项目，我将把我的称为`SQLiteIntro`。

这个应用程序不会很复杂，因为我只是想对这个主题做一个简单的介绍，让你对在 Swift 项目中使用 SQL 有个大概的了解。

# 包装材料

在专用的类/结构中分离逻辑是一个很好的实践。在这种情况下，因为我们使用的是 SQL 数据库，所以我想创建一个类来抽象一点数据层逻辑，这样代码会更加简洁。

```
final class DataWrapper: ObservableObject {
    private let db: FMDatabase

    init(fileName: String = "test") {
        // 1 - Get filePath of the SQLite file
        let fileURL = try! FileManager.default
            .url(for: .applicationSupportDirectory, in: .userDomainMask, appropriateFor: nil, create: true)
            .appendingPathComponent("\(fileName).sqlite")

        // 2 - Create FMDatabase from filePath
        let db = FMDatabase(url: fileURL)

        // 3 - Open connection to database
        guard db.open() else {
            fatalError("Unable to open database")
        }

        // 4 - Initial table creation
        do {
            try db.executeUpdate("create table if not exists users(username varchar(255) primary key, age integer)", values: nil)
        } catch {
            fatalError("cannot execute query")
        }

        self.db = db
    }
}
```

这就是你需要开始的，简单吧？

代码很简单:当最初创建`DataWrapper`类时，它将查找数据库文件，如果文件不存在，FMDB 将使用该路径为您创建一个数据库。最后，它打开一个到数据库的连接并创建`user`表。

# 模型

为了处理数据库记录，我想创建一个`User`结构，在我的例子中，我将包含一些其他与 JSON 相关的东西，因为我稍后将使用它通过一些 web APIs 创建具有随机名称的用户。

```
struct User: Hashable, Decodable {
    let username: String
    let age: Int

    init(username: String, age: Int) {
        self.username = username
        self.age = age
    }

    init?(from result: FMResultSet) {
        if let username = result.string(forColumn: "username") {
            self.username = username
            self.age = Int(result.int(forColumn: "age"))
        } else {
            return nil
        }
    }

    private enum CodingKeys : String, CodingKey {
        case username = "first_name"
    }

    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        username = try container.decode(String.self, forKey: .username)
        age = Int.random(in: 1..<100)
    }
}
```

`FMResultSet`是您从数据库执行查询时总是会得到的结果，即使您期望的是单个记录或空结果，这就是为什么在这种情况下有一个专用的 init 函数来处理所有设置逻辑是有用的。

# 联合收割机和 MVVM

因为我正在使用 SwiftUI，所以我希望`DataWrapper`是反应式的，并通知
数据库中可能发生的变化。回到`DataWrapper`的实现，我添加了一个`@Published`用户数组，这样我可以在`List`中显示他们。

```
final class DataWrapper: ObservableObject {
    private let db: FMDatabase
    @Published var users = [User]()
    ...}
```

为了从数据库中获取用户并在数据库打开后立即发布他们，我们需要创建一个方法来查询所有用户，并在数据库初始化后将他们设置为`DataWrapper`的 users 变量。

```
func getAllUsers() -> [User] {
    var users = [User]()
    do {
        let result = try db.executeQuery("select username, age from users", values: nil)
        while result.next() {
            if let user = User(from: result) {
                users.append(user)
            }
        }
        return users
    } catch {
        return users
    }
}
```

并将该调用放在`DataWrapper`的`init`方法的底部

```
users = getAllUsers()
```

现在，当你第一次启动`DataWrapper`时，它将自动获取所有用户，并且他们将准备好在 SwiftUI 中使用。

我将创建一个稍后会用到的`insert`函数:

```
func insert(_ user: User) {
    do {
        try db.executeUpdate(
            """
            insert into users (username, age)
            values (?, ?)
            """,
            values: [user.username, user.age]
        )
        users.append(user)
    } catch {
        fatalError("cannot insert user: \(error)")
    }
}
```

# 快速浏览视图

我想创建一个`List`来显示数据库包含的所有用户，并创建一个简单的函数来查询 web API 以获得一个随机用户名，并将一个新用户插入到数据库中。

```
struct ContentView: View {
    [@EnvironmentObject](http://twitter.com/EnvironmentObject) var db: DataWrapper

    var body: some View {
        NavigationView {
            List(db.users, id: \.self) { user in
                HStack {
                    Text(user.username)
                    Spacer()
                    Text("\(user.age)")
                }
            }

            .navigationTitle("Users")
            .toolbar {
                ToolbarItem(id: "plus", placement: .navigationBarTrailing, showsByDefault: true) {
                    Button(action: {
                        createRandomUser()
                    }, label: {
                        Image(systemName: "plus")
                    })
                }
            }
        }
    }

    private func createRandomUser() {
        let url = URL(string: "[https://random-data-api.com/api/name/random_name](https://random-data-api.com/api/name/random_name)")!
        let task = URLSession.shared.dataTask(with: url) { data, response, error in
            guard let data = data else {
                fatalError("No data")
            }

            DispatchQueue.main.async {
                let user = try! JSONDecoder().decode(User.self, from: data)
                db.insert(user)
            }
        }
        task.resume()
    }
}
```

如果你现在运行你的应用程序，你会看到一个空列表，但如果你按下加号按钮，你将开始在数据库中插入东西，名字将开始反应性地出现在你的列表中。

# 结论

这是一个非常简单的场景，现在可能显示 CoreData 版本的性能有了巨大的提升，但这是一种在更熟悉的 SQLite 数据库中存储数据的不同方式。

如果您希望对数据有更多的控制，SQLite 和 SQL 的强大功能肯定不会让您失望。SQLite 可以提高需要细粒度控制和目标查询优化的应用程序的性能。使用 CloudKit 同步数据也更容易，因为现在您只需同步 SQLite 文件，而无需处理所有的 CoreData 表和不同的版本。

我正在写一篇关于 SQLite 迁移策略的文章，
所以如果你想了解更多，请继续关注！

```
**Want to Connect?**This article is also available on my [website](http://mattrighetti.com).
```