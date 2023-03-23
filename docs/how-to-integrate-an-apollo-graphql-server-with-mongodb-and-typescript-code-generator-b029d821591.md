# 如何将 Apollo GraphQL 服务器与 MongoDB 和 TypeScript 代码生成器集成在一起(第 2 部分)

> 原文：<https://betterprogramming.pub/how-to-integrate-an-apollo-graphql-server-with-mongodb-and-typescript-code-generator-b029d821591>

## Apollo GraphQL 服务器与 TypeScript 系列的第二部分

![](img/b5d23abe54af56d843adea234677cb05.png)

[日立树](https://en.wikipedia.org/wiki/Moanalua_Gardens)(图片来源:[方志伟](http://derekfong.me))

这是 *Apollo GraphQL: How-To* 系列的**部分 2** :

*   **第 1 部分:** [如何用 TypeScript 和 Webpack 热模块替换构建 Apollo GraphQL 服务器](https://medium.com/free-code-camp/build-an-apollo-graphql-server-with-typescript-and-webpack-hot-module-replacement-hmr-3c339d05184f)
*   **第 2 部分:** **如何将 Apollo GraphQL 服务器与 MongoDB 和 TypeScript 代码生成器集成在一起**
*   …更多即将推出！

让我们将 Apollo GraphQL Server 与 MongoDB 和 TypeScript Codegen 集成起来！

在我的上一篇[文章](https://medium.com/free-code-camp/build-an-apollo-graphql-server-with-typescript-and-webpack-hot-module-replacement-hmr-3c339d05184f)中，我展示了如何使用我在产品应用中使用的一套工具和设计概念来构建一个“Hello Word”Apollo 服务器。在这篇文章中，我们将继续这个话题，配置 Apollo 服务器与 MongoDB(或您选择的任何数据库)通信，并利用 [GraphQL 代码生成器](https://graphql-code-generator.com)来改进开发工作流。

事不宜迟，我们开始吧！

# 1.召唤希腊神

在这篇文章中，我们将使用我上一篇文章中的“Hello World”阿波罗服务器[作为起点。你可以](https://medium.com/free-code-camp/build-an-apollo-graphql-server-with-typescript-and-webpack-hot-module-replacement-hmr-3c339d05184f)[从 GitHub](https://github.com/derek-fong/demo-apollo-server-webpack-hmr) 克隆我的库，或者如果你需要任何设置的详细说明，请参考[我的上一篇帖子](https://github.com/derek-fong/demo-apollo-server-webpack-hmr)。

一旦[克隆了存储库](https://github.com/derek-fong/demo-apollo-server-webpack-hmr)，快速测试“Hello World”Apollo 服务器是否运行:

**1。安装包依赖项:**

```
$ npm install
```

2.在项目根目录下添加一个新的`.env`文件:

文件:`.env`

```
APOLLO_INTROSPECTION=true
APOLLO_PLAYGROUND=true
```

3.在`development`模式下构建应用程序:

```
$ NODE_ENV=development npm run build
```

4.打开一个新的终端，用`.env`启动 Apollo 服务器。

```
$ npm run start:env
```

5.一旦服务器启动并运行，启动 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground/) (默认为 [http://localhost:4000](http://localhost:4000) )并运行一个测试查询。

```
{
  testMessage
}
```

您应该看到服务器返回查询结果:

```
{
  "data": {
    "testMessage": "Hello World!"
  }
}
```

酷！我们现在已经确认我们的服务器已经启动并运行。但是它还没有做太多。

让我们把它变得更有用，更有趣！

# 2.故事

目前，我们的 Apollo 服务器将总是为每个`testMessage`查询返回硬编码的`Hello World!`——这有点无聊。我们将把我们的“Hello Word”Apollo 服务器转换成简化的[中型](https://medium.com/)克隆:

*   `User`包含`first name`、`last name`、`e-mail address`等个人详细信息。
*   `Post`包含一个`title`、`content`、`published date`和`author`。
*   `User`可以发布任意数量的`posts`。
*   `User`可以跟随其他`users`。
*   `User`可以喜欢任何`posts`。

基于上面的描述，我们可以开始围绕`Users`和`Posts`实体构建我们的 Apollo 服务器，有一组`[queries](https://graphql.org/learn/queries/)` [和](https://graphql.org/learn/queries/) `[mutations](https://graphql.org/learn/queries/)`可以与之交互。

![](img/40980a9703145ce0f6d8aa593dbd0af2.png)

人生是一段旅程(图片来源: [rawpixel](https://www.rawpixel.com) )

# 3.模式优先！

现在我们已经有了构建什么的想法，是时候将我们的想法收集到代码中了！

概括地说，GraphQL 服务器由两个主要组件组成:

*   [**模式**](https://www.apollographql.com/docs/apollo-server/schema/schema) : *什么类型的数据可用。*
*   [**解析器**](https://www.apollographql.com/docs/apollo-server/data/data) : *如何构造*数据。

让我们从定义服务器的[模式](https://www.apollographql.com/docs/apollo-server/schema/schema)开始。

在我的上一篇文章中，我们在名为`type-defs.ts`的类型脚本文件中定义了我们的模式。在使用 TypeScript 编写模式的同时，我们也可以在`.graphql`文件中编写我们的模式，以获得更好的 IDE 支持，这要感谢使用 `[graphql-tag/loader](https://github.com/apollographql/graphql-tag#webpack-preprocessing-with-graphql-tagloader)`的 [Webpack 处理。如果你跟随上一篇文章](https://github.com/apollographql/graphql-tag#webpack-preprocessing-with-graphql-tagloader)中的[我的示例代码，那么`.graphql` Webpack 加载器](https://github.com/derek-fong/demo-apollo-server-webpack-hmr)[已经预先配置好了](https://github.com/derek-fong/demo-apollo-server-webpack-hmr/blob/master/webpack.common.js#L11-L15)。如果没有，只需遵循本指南中的几行代码就可以启用 Webpack `.graphql`预处理。

现在，让我们删除`/src`文件夹中的`type-def.ts`和`resolvers.ts`，并创建一个名为`type-defs.graphql`的文件。

文件:`src/type-defs.graphql`

```
type User {
  """
  User ID.
  """
  id: ID!

  """
  User's first name.
  """
  firstName: String!

  """
  User's last name.
  """
  lastName: String!

  # User's e-mail address.
  # email: TODO: Define type.

  """
  Posts published by user.
  """
  posts: [Post]

  """
  Users that this user is following.
  """
  following: [User]

  """
  Users that this user is followed by.
  """
  followers: [User]
}

type Post {
  """
  Post ID.
  """
  id: ID!

  """
  Post title.
  """
  title: String!

  """
  Post content.
  """
  content: String!

  """
  Post Author.
  """
  author: User!

  # Post published timestamp.
  # publishedAt: TODO: Define type.

  """
  Users who like this post.
  """
  likedBy: [User]
}

type Query {
  """
  Get post by ID.
  """
  post(id: ID!): Post
}

"""
Publish post input.
"""
input PublishPostInput {
  """
  Post title.
  """
  title: String!

  """
  Post content.
  """
  content: String!
}

type Mutation {
  """
  Publish post.
  """
  publishPost(input: PublishPostInput!): Post!

  """
  Follow user.
  Returns the updated number of followers.
  """
  followUser(
    """
    User's ID to follow.
    """
    userId: ID!
  ): Int!

  """
  Unfollow user.
  Returns the updated number of followers.
  """
  unfollowUser(
    """
    User's ID to unfollow.
    """
    userId: ID!
  ): Int!

  """
  Like post.
  Returns the updated number of likes received.
  """
  likePost(
    """
    Post's ID to like.
    """
    postId: ID!
  ): Int!
}
```

然后更新`main.ts`。

文件:`src/main.ts`

```
import { ApolloServer } from 'apollo-server';

import { environment } from './environment';
import * as typeDefs from './type-defs.graphql';

const server = new ApolloServer({
  typeDefs,
  introspection: environment.apollo.introspection,
  mocks: true, // TODO: Remove in PROD.
  mockEntireSchema: false, // TODO: Remove in PROD.
  playground: environment.apollo.playground,
});

server
  .listen(environment.port)
  .then(({ url }) => console.log(`Server ready at ${url}. `));

if (module.hot) {
  module.hot.accept();
  module.hot.dispose(() => server.stop());
}
```

*   第 4 行:我们从一个`.graphql`文件中导入类型定义。
*   **第 9 行:**我们从 Apollo 服务器启用了[默认模仿](https://www.apollographql.com/docs/apollo-server/testing/mocking/#default-mock-example)特性。当我们想快速测试我们的 GraphQL 模式而不需要编写一行解析器代码时，这非常方便！
*   **第 10 行:**虽然我们已经启用了模拟，但是我们已经[禁用了模拟整个模式](https://www.apollographql.com/docs/apollo-server/api/apollo-server)。如果解析器被定义为完成一个查询(或变异)，Apollo Server 将尝试返回这些值，并将只为无法解析的字段返回一个模拟值。这允许我们增量地实现我们的解析器(您将在本文的后面看到这一点)。

现在，如果您尝试构建应用程序，将会遇到以下错误:

```
$ NODE_ENV=development npm run build> TS2307: Cannot find module './type-defs.graphql'.
```

为了解决这个问题，[在](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf) `[/src](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf)` [目录](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf)中添加一个名为 `[graphql.d.ts](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf)` [的文件:](https://dev.to/open-graphql/how-to-resolve-import-for-the-graphql-file-with-typescript-and-webpack-35lf)

文件:`src/graphql.d.ts`

```
declare module '*.graphql' {
  import { DocumentNode } from 'graphql';

  const value: DocumentNode;
  export = value;
}
```

如果你重新运行`NODE_ENV=development npm run build`和`npm run start:env`，阿波罗服务器应该再次启动并运行。

现在，让我们通过在 Apollo 服务器的 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground) 中运行以下查询来测试我们的模式:

```
{
  post(id: "TEST_ID") {
    id
    title
    content
    author {
      id
      firstName
      lastName
      followers {
        id
        firstName
        lastName
      }
    }
  }
}
```

它应该会返回类似如下的结果:

```
{
  "data": {
    "post": {
      "id": "2e6530bc-aa08-4222-b666-9984dfcabbe5",
      "title": "Hello World",
      "content": "Hello World",
      "author": {
        "id": "92f90af0-3388-4c2b-8484-80a116cfe2a5",
        "firstName": "Hello World",
        "lastName": "Hello World",
        "followers": [
          {
            "id": "0efca26d-6857-4af6-8373-79b406891992",
            "firstName": "Hello World",
            "lastName": "Hello World"
          },
          {
            "id": "952fabad-cbdd-47cb-badd-ae3678ec3c4b",
            "firstName": "Hello World",
            "lastName": "Hello World"
          }
        ]
      }
    }
  }
}
```

`String`字段由一些随机文本填充(在本例中为 *Hello World* )，而`id`字段由随机的`[UUID](https://tools.ietf.org/html/rfc4122)`填充。我们通过启用 Apollo Server 的[模仿](https://www.apollographql.com/docs/apollo-server/testing/mocking)功能实现了这一点，甚至无需编写一行解析器代码。神奇！

# 4.添加自定义标量

在这个阶段，我们的模式由具有 GraphQL 的[基本类型](https://graphql.org/graphql-js/basic-types)的字段组成。但是像`published date`、`e-mail address`等字段呢？

虽然您可以自己实现[自定义标量](https://www.apollographql.com/docs/apollo-server/schema/scalars-enums/#gatsby-focus-wrapper)，但是当有人已经为您做了艰苦的工作时，为什么还要重新发明轮子呢？

查看`[graphql-scalars](https://github.com/Urigo/graphql-scalars)`—“*自定义 GraphQL 标量库，用于创建精确的类型安全 GraphQL 模式”*。

![](img/38d2afe9359de2e53a280da4f4e7853f.png)

GraphQL 标量(图片来源: [GitHub](https://github.com/Urigo/graphql-scalars) )

我们将使用这个库中的一些标量。让我们安装这个包。

```
$ npm install --save graphql-scalars
```

该库的 README 非常简单明了，但是让我们一起完成这些步骤，以确保我们在同一页上。

## **1。向模式添加标量**

在我们的模式文件`type-defs.graphql`中，用语法`scalar TypeName`声明`DateTime`和`EmailAddress`标量。然后我们可以在`User`的`email`字段和`Post`的`publishedAt`字段中使用标量类型。

对于突变，我们将`followUser`、`unfollowUser`和`likePost`的返回类型从`Int`替换为`UnsignedInt`，因为计数必须为非负整数。

文件:`src/type-defs.graphql`

```
scalar DateTime
scalar EmailAddress
scalar UnsignedInt

type User {
  """
  User ID.
  """
  id: ID!

  """
  User's first name.
  """
  firstName: String!

  """
  User's last name.
  """
  lastName: String!

  """
  User's e-mail address.
  """
  email: EmailAddress

  """
  Posts published by user.
  """
  posts: [Post]

  """
  Users that this user is following.
  """
  following: [User]

  """
  Users that this user is followed by.
  """
  followers: [User]
}

type Post {
  """
  Post ID.
  """
  id: ID!

  """
  Post title.
  """
  title: String!

  """
  Post content.
  """
  content: String!

  """
  Post Author.
  """
  author: User!

  """
  Post published timestamp.
  """
  publishedAt: DateTime

  """
  Users who like this post.
  """
  likedBy: [User]
}

type Query {
  """
  Get post by ID.
  """
  post(id: ID!): Post
}

"""
Publish post input.
"""
input PublishPostInput {
  """
  Post title.
  """
  title: String!

  """
  Post content.
  """
  content: String!
}

type Mutation {
  """
  Publish post.
  """
  publishPost(input: PublishPostInput!): Post!

  """
  Follow user.
  Returns the updated number of followers.
  """
  followUser(
    """
    User's ID to follow.
    """
    userId: ID!
  ): UnsignedInt!

  """
  Unfollow user.
  Returns the updated number of followers.
  """
  unfollowUser(
    """
    User's ID to unfollow.
    """
    userId: ID!
  ): UnsignedInt!

  """
  Like post.
  Returns the updated number of likes received.
  """
  likePost(
    """
    Post's ID to like.
    """
    postId: ID!
  ): UnsignedInt!
}
```

## **2。向解析器映射添加标量**

在上一步中，我们声明了*模式上可用的*标量类型。现在我们需要告诉服务器*如何*解析这些标量类型。

在`/src`目录中创建一个解析器映射文件`resolvers.ts`。

文件:`src/resolvers.ts`

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
};
```

## **3。给阿波罗服务器添加模拟**

因为我们目前正在 Apollo Server 中使用[模拟](https://www.apollographql.com/docs/apollo-server/testing/mocking)，我们需要告诉 Apollo Server [如何模拟标量](https://github.com/Urigo/graphql-scalars/blob/master/README.md#using-mocks-with-apollo-server)。一旦我们完成了所有必需的解析器的实现，我们将移除这些模拟。

文件:`src/main.ts`

```
import { ApolloServer } from 'apollo-server';
import {
  DateTimeMock,
  EmailAddressMock,
  UnsignedIntMock,
} from 'graphql-scalars';

import { environment } from './environment';
import { resolvers } from './resolvers';
import * as typeDefs from './type-defs.graphql';

const server = new ApolloServer({
  resolvers,
  typeDefs,
  introspection: environment.apollo.introspection,
  mocks: {
    DateTime: DateTimeMock,
    EmailAddress: EmailAddressMock,
    UnsignedInt: UnsignedIntMock,
  }, // TODO: Remove in PROD.
  mockEntireSchema: false, // TODO: Remove in PROD.
  playground: environment.apollo.playground,
});

server
  .listen(environment.port)
  .then(({ url }) => console.log(`Server ready at ${url}. `));

if (module.hot) {
  module.hot.accept();
  module.hot.dispose(() => server.stop());
}
```

**第 17–19 行:**定义如何模拟标量。

## **4。测试行动中的标量模拟**

让我们用标量模拟来测试我们的阿波罗服务器！运行`NODE_ENV=development npm run build`和`npm run start:env`来启动阿波罗服务器，如果你还没有这么做的话。

如果您尝试在 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground) 中运行下面的[查询](https://graphql.org/learn/queries):

```
{
  post(id:"TEST_POST_ID") {
    title
    content
    publishedAt
    author {
      id
      firstName
      lastName
      email
    }
  }
}
```

它应该会返回类似如下的结果:

```
{
  "data": {
    "post": {
      "title": "Hello World",
      "content": "Hello World",
      "publishedAt": "2019-12-02T01:12:00.780Z",
      "author": {
        "id": "61ad57ab-2cb6-46f9-b35a-a54c5bf610c3",
        "firstName": "Hello World",
        "lastName": "Hello World",
        "email": "[test@test.com](mailto:test@test.com)"
      }
    }
  }
}
```

如果你试图运行一个[突变](https://graphql.org/learn/queries/#mutations):

```
mutation TestLikePost {
  likePost(postId: "TEST_POST_ID") 
}
```

它应该返回一个非负整数[:](https://github.com/Urigo/graphql-scalars#nonnegativeint)

```
{
  "data": {
    "likePost": 123
  }
}
```

不错！

# 5.MongoDB 简介

现在我们已经有了一个描述性的模式，是时候将 [MongoDB](https://www.mongodb.com) 添加进来并开始实现解析器了。

## **1。安装 MongoDB**

如果您还没有安装 MongoDB 到您的本地环境中，请安装它。或者，如果你喜欢云数据库解决方案，你可以使用 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 。MongoDB Atlas 提供了一个[免费等级](https://www.mongodb.com/cloud/atlas/pricing)供试用。

## **2。为 Node.js 安装** [**MongoDB 驱动**](https://github.com/mongodb/node-mongodb-native) **。**

这里没什么特别的。只需[安装 MongoDB NPM 包](https://github.com/mongodb/node-mongodb-native#installation)和 MongoDB 的[类型脚本类型](https://basarat.gitbooks.io/typescript/docs/types/@types.html)。

```
$ npm install --save mongodb
$ npm install --save-dev @types/mongodb
```

## **3。在环境变量中指定数据库细节**

首先，让我们更新`environment.ts`来接受 MongoDB 连接细节。

文件:`src/environment.ts`

```
const defaultPort = 4000;

interface Environment {
  apollo: {
    introspection: boolean;
    playground: boolean;
  };
  mongoDb: {
    databaseName: string;
    url: string;
  };
  port: number | string;
}

export const environment: Environment = {
  apollo: {
    introspection: process.env.APOLLO_INTROSPECTION === 'true',
    playground: process.env.APOLLO_PLAYGROUND === 'true',
  },
  mongoDb: {
    databaseName: process.env.MONGODB_DB_NAME as string,
    url: process.env.MONGODB_URL as string,
  },
  port: process.env.PORT || defaultPort,
};
```

然后将数据库名称和 MongoDB 的 URL 添加到`.env`的`MONGODB_DB_NAME`和`MONGODB_URL`中。

文件:`.env`

```
APOLLO_INTROSPECTION=true
APOLLO_PLAYGROUND=true
MONGODB_DB_NAME=graphqlMongodbDemo
MONGODB_URL=mongodb://localhost:27017
```

## **4。添加 MongoDB 提供者**

我们将实现一个提供者(或助手)来帮助执行一些常见的数据库操作。

我们还将实现一个种子函数`addMockUsersAsync()`来将一些模拟用户插入到 MongoDB 的`users`集合中进行测试。

文件:`src/mongodb.provider.ts`

```
import { Collection, Db, MongoClient, ObjectID } from 'mongodb';

import { environment } from './environment';

export class MongoDbProvider {
  private database?: Db;
  private mongoClient: MongoClient;

  constructor(url: string) {
    this.mongoClient = new MongoClient(url, { useUnifiedTopology: true });
  }

  get postsCollection(): Collection {
    const postsCollection = this.getCollection('posts');

    if (!postsCollection) {
      throw new Error('Posts collection is undefined');
    }

    return postsCollection;
  }

  get usersCollection(): Collection {
    const usersCollection = this.getCollection('users');

    if (!usersCollection) {
      throw new Error('Users collection is undefined');
    }

    return usersCollection;
  }

  /**
   * Connect to MongoDB.
   * @async
   * @param databaseName - Database name.
   */
  async connectAsync(databaseName: string): Promise<void> {
    await this.mongoClient.connect();
    this.database = this.mongoClient.db(databaseName);
  }

  /**
   * Close the database and its underlying connections.
   */
  async closeAsync(): Promise<void> {
    await this.mongoClient.close();
  }

  /**
   * Fetch a specific collection.
   * @private
   * @param collectionName - Collection name.
   * @returns The collection instance.
   */
  private getCollection(collectionName: string): Collection {
    if (!this.database) {
      throw new Error('Database is undefined.');
    }

    return this.database.collection(collectionName);
  }
}

export const mongoDbProvider = new MongoDbProvider(environment.mongoDb.url);

/**
 * Add mock users if `users` collection is empty.
 * TODO: Remove in Production.
 */
export async function addMockUsersAsync(): Promise<void> {
  const usersCount = await mongoDbProvider.usersCollection.countDocuments();

  if (usersCount === 0) {
    await mongoDbProvider.usersCollection.insertMany([
      {
        _id: new ObjectID('0123456789abcdef01234567'),
        firstName: 'Test',
        lastName: 'User 1',
        email: 'test.user1@test.com',
      },
      {
        _id: new ObjectID('fedcba987654321098765432'),
        firstName: 'Test',
        lastName: 'User 2',
        email: 'test.user2@test.com',
        following: [new ObjectID('0123456789abcdef01234567')],
      },
    ]);
  }
}
```

## **5。在 Apollo 服务器启动时连接到 MongoDB**

更新`main.ts`在引导期间连接 MongoDB。

文件:`src/main.ts`

```
import { ApolloServer } from 'apollo-server';
import { DateTimeMock, EmailAddressMock } from 'graphql-scalars';

import { environment } from './environment';
import { addMockUsersAsync, mongoDbProvider } from './mongodb.provider';
import * as typeDefs from './type-defs.graphql';

(async function bootstrapAsync(): Promise<void> {
  await mongoDbProvider.connectAsync(environment.mongoDb.databaseName);
  await addMockUsersAsync(); // TODO: Remove in PROD.

  const server = new ApolloServer({
    typeDefs,
    introspection: environment.apollo.introspection,
    mockEntireSchema: false, // TODO: Remove in PROD.
    mocks: {
      DateTime: DateTimeMock,
      EmailAddress: EmailAddressMock,
      UnsignedInt: UnsignedIntMock,
    }, // TODO: Remove in PROD.
    playground: environment.apollo.playground,
  });

  server
    .listen(environment.port)
    .then(({ url }) => console.log(`Server ready at ${url}. `));

  if (module.hot) {
    module.hot.accept();
    module.hot.dispose(async () => {
      server.stop();
      await mongoDbProvider.closeAsync();
    });
  }
})();
```

*   **第 8–34 行**:我们使用了[异步/等待](https://basarat.gitbooks.io/typescript/docs/async-await.html)，因此我们用异步函数和[生命周期](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)包装了引导动作。
*   **第 9 行:** [将](https://mongodb.github.io/node-mongodb-native/api-generated/mongoclient.html#connect) s 连接到`[.env](https://github.com/motdotla/dotenv)`环境变量文件中指定的 MongoDB 的数据库。
*   **第 10 行:**如果集合为空，则将模拟用户添加到 MongoDB 的`users`集合中。
*   **第 31 行:**当 Webpack 的 [HMR](https://webpack.js.org/concepts/hot-module-replacement) 启动时，优雅地关闭与 MongoDB 的连接。

## **6。启动 MongoDB 守护进程**

在我们启动 [MongoDB](https://docs.mongodb.com/manual/reference/program/mongod) 之前，我们需要一个目录来存储数据库相关的文件，例如`tmp/db`:

```
# Create a folder to store database files. 
$ mkdir -p tmp/db# Start MongoDB daemon process. 
$ mongod --dbpath tmp/db
```

## **7。构建并启动阿波罗服务器**

一旦 MongoDB 启动，以`development`模式构建并启动 Apollo 服务器。

```
$ NODE_ENV=development npm run build
$ npm run start:env> Server ready at [http://localhost:4000/](http://localhost:4000/).
```

## **8。测试直接查询 MongoDB**

如果实现正确，应该有两个被模仿的用户对象被添加到 MongoDB 的`users`集合中(通过在服务器引导上执行`addMockUsersAsync()`)。然而，由于我们还没有在我们的 Apollo 服务器中实现任何解析器，我们还不能用 Apollo 服务器测试它。

为了验证我们刚刚添加的代码正在工作，我们可以用 [mongo shell](https://docs.mongodb.com/manual/mongo) 直接查询 MongoDB。

打开一个新命令行并运行以下命令:

```
# Open mongo shell. 
$ mongo# Show available MongoDB databases. 
$ show databases> admin               0.000GB
> config              0.000GB
> graphqlMongodbDemo  0.000GB
> local               0.000GB# Use database `graphqlMongodbDemo`. 
$ use graphqlMongodbDemo# Get all users from `users` collection. 
$ db.users.find({})# It should return two `users` objects if implemented correctly. 
> { "_id" : ObjectId("0123456789abcdef01234567"), "firstName" : "Test", "lastName" : "User 1", "email" : "[test.user1@test.com](mailto:test.user1@test.com)" }
{ "_id" : ObjectId("fedcba987654321098765432"), "firstName" : "Test", "lastName" : "User 2", "email" : "[test.user2@test.com](mailto:test.user2@test.com)", "following" : [ ObjectId("0123456789abcdef01234567") ] }# Exit mongo shell. 
$ exit
```

**注意**:如果你喜欢用 GUI 管理 MongoDBs，你也可以使用像 [Robo 3T](https://robomongo.org) 这样的工具。

既然我们已经成功地将 Apollo 服务器连接到 MongoDB，我们的下一步就是开始实现解析器来处理“真实”数据。

但是在我们开始实现解析器之前，我们需要描述我们的实体(`Users`、`Posts`等等)。)中。我们已经用 GraphQL 模式描述了我们的类型。在 TypeScript 中编写类型定义不仅看起来工作量增加了一倍，而且更容易出错——可以在 GraphQL 模式中更新类型，但却忘记了更新 TypeScript 类型定义。

一定有更聪明的方法来处理这件事！

# 6.GraphQL 代码生成器简介

![](img/a36054460937dc2abef6756ac5f3f9ec.png)

GraphQL 代码生成器(图片来源: [GitHub](https://github.com/dotansimha/graphql-code-generator) )

GraphQL 代码生成器是一个 *CLI 工具，可以从 GraphQL 模式*中生成打字稿。 [GraphQL codegen](https://graphql-code-generator.com) 作为插件出现在 [graphql-cli](https://github.com/urigo/graphql-cli) 工具集中。要使用 [GraphQL codegen](https://graphql-code-generator.com) :

## **1。安装 graphql-cli** 的 [***金丝雀*版本**](https://github.com/Urigo/graphql-cli#install)

```
$ npm install --save graphql-cli@canary
```

## **2。为 GraphQL codegen** 安装[**graph QL codegen**](https://graphql-code-generator.com)**插件和** [**TypeScript 插件**](https://www.npmjs.com/package/@graphql-codegen/typescript)

```
$ npm install --save @test-graphql-cli/codegen@canary @graphql-codegen/typescript
```

**注意**:除了 [GraphQL codegen](https://graphql-code-generator.com) ， [GraphQL CLI 还附带了一个有用插件的列表](https://github.com/Urigo/graphql-cli#plugin-system)。请务必试用它们！

## **3。创建** [**GraphQL 配置**](https://graphql-config.com) **文件**

在项目根目录下创建一个名为`.graphqlrc.yml`的 [GraphQL 配置](https://graphql-config.com)文件:

文件:`.graphqlrc.yml`

```
schema: ./src/**/*.graphql
extensions:
  codegen:
    ./src/graphql-codegen-typings.d.ts:
      plugins:
        - typescript
```

*   **第 1 行:**包含`/src`目录中所有文件扩展名为`.graphql`的 GraphQL 模式文件。在本例中，我们只有一个模式文件:`typedefs.graphql`。
*   **第 3–6 行:**包含 [codegen](https://graphql-code-generator.com) 插件，指定输出 TypeScript 类型定义文件为`src/graphql-codegen-typings.d.ts`。

**注**:关于设置的更多细节，请参考 [GraphQL 配置文档](https://graphql-config.com/docs/introduction)。

## **4。在构建和测试之前运行 graph QL codegen**

我们添加了 [NPM 脚本](https://docs.npmjs.com/misc/scripts)来在每个应用构建或测试之前运行 GraphQL codegen。

文件:`package.json`

```
{
  ...
  "scripts": {
    "build": "webpack --config webpack.$NODE_ENV.js",
    "graphql:codegen": "graphql codegen",
    "prebuild": "npm run graphql:codegen",
    "pretest": "npm run graphql:codegen",
    "start": "node dist/server",
    "start:env": "node --require dotenv/config dist/server",
    "test": "jest"
  },
  "dependencies": { ... },
  "devDependencies": { ... }
}
```

*   **第 5 行:**定义 [GraphQL CLI](https://github.com/Urigo/graphql-cli#plugin-system) 运行`[codegen](https://graphql-code-generator.com)`。
*   **第 6 行:**利用 [NPM 的*前*钩](https://medium.com/yld-blog/using-npm-pre-and-post-hooks-d89dcf2d86cf)触发`graphql codegen`在`build`之前运行。
*   **7 线:**与`prebuild`和`build`类似，`graphql codegen`会在`test`运行前触发。

## **5。在操作中生成类型脚本类型定义**

通过这种设置，每次运行`npm run build`时，都会在`/src`目录中生成一个`graphql-codegen-typings.d.ts`文件。生成的文件包含与 GraphQL 模式中定义的所有类型相匹配的 TypeScript 类型定义。使用这种方法，我们可以确保我们的 TypeScript 类型与 GraphQL 模式一致。

这很好……但是 GraphQL 模式中描述的生成类型并不总是反映数据是如何存储在数据库中的。当使用解析器时，我们需要处理类型与 GraphQL 模式紧密匹配的对象，以及数据库模型。我们如何实现这一点？

![](img/e315edc9ba5c095613621d308b685cb2.png)

(图片来源:你知道的——在亚马逊上)

# 7。使用 GraphQL Codegen 生成数据库对象类型

幸运的是，MongoDB 有一个 [GraphQL codegen 插件可以用 MongoDB 模型生成 TypeScript 类型。](https://graphql-code-generator.com/docs/plugins/typescript-mongodb)

## **1。安装** `**typescript-mongodb**` **插件**

```
$ npm install --save @graphql-codegen/typescript-mongodb
```

## **2。将** `**typescript-mongodb**` **添加到** [**GraphQL 配置**](https://graphql-config.com) **文件**

文件:`.graphqlrc.yml`

```
{
  ...
  "scripts": {
    "build": "webpack --config webpack.$NODE_ENV.js",
    "graphql:codegen": "graphql codegen",
    "prebuild": "npm run graphql:codegen",
    "pretest": "npm run graphql:codegen",
    "start": "node dist/server",
    "start:env": "node --require dotenv/config dist/server",
    "test": "jest"
  },
  "dependencies": { ... },
  "devDependencies": { ... }
}
```

## **3。使用 GraphQL 模式指令描述 MongoDB 模型**

[按照插件指令](https://graphql-code-generator.com/docs/plugins/typescript-mongodb#usage)用指令描述你的`Post`和`User`实体。

文件:`src/type-defs.graphql`

```
scalar DateTime
scalar EmailAddress
scalar UnsignedInt

type User @entity {
  """
  User ID.
  """
  id: ID! @id

  """
  User's first name.
  """
  firstName: String! @column

  """
  User's last name.
  """
  lastName: String! @column

  """
  User's e-mail address.
  """
  email: EmailAddress @column(overrideType: "string")

  """
  Posts published by user.
  """
  posts: [Post]

  """
  Users that this user is following.
  """
  following: [User] @link

  """
  Users that this user is followed by.
  """
  followers: [User]
}

type Post @entity {
  """
  Post ID.
  """
  id: ID! @id

  """
  Post title.
  """
  title: String! @column

  """
  Post content.
  """
  content: String! @column

  """
  Post Author.
  """
  author: User! @link

  """
  Post published timestamp.
  """
  publishedAt: DateTime @column(overrideType: "Date")

  """
  Users who like this post.
  """
  likedBy: [User] @link
}

type Query {
  """
  Get post by ID.
  """
  post(id: ID!): Post
}

"""
Publish post input.
"""
input PublishPostInput {
  """
  Post title.
  """
  title: String!

  """
  Post content.
  """
  content: String!
}

type Mutation {
  """
  Publish post.
  """
  publishPost(input: PublishPostInput!): Post!

  """
  Follow user.
  Returns the updated number of followers.
  """
  followUser(
    """
    User's ID to follow.
    """
    userId: ID!
  ): UnsignedInt!

  """
  Unfollow user.
  Returns the updated number of followers.
  """
  unfollowUser(
    """
    User's ID to unfollow.
    """
    userId: ID!
  ): UnsignedInt!

  """
  Like post.
  Returns the updated number of likes received.
  """
  likePost(
    """
    Post's ID to like.
    """
    postId: ID!
  ): UnsignedInt!
}
```

不要忘记[将指令声明](https://graphql-code-generator.com/docs/plugins/typescript-mongodb#usage)添加到 GraphQL 模式定义中。

文件:`src/main.ts`

```
import { DIRECTIVES } from '@graphql-codegen/typescript-mongodb';
import { ApolloServer } from 'apollo-server';
import { DateTimeMock, EmailAddressMock } from 'graphql-scalars';

import { environment } from './environment';
import { addMockUsersAsync, mongoDbProvider } from './mongodb.provider';
import * as typeDefs from './type-defs.graphql';

(async function bootstrapAsync(): Promise<void> {
  await mongoDbProvider.connectAsync(environment.mongoDb.databaseName);
  await addMockUsersAsync(); // TODO: Remove in Production.

  const server = new ApolloServer({
    typeDefs: [DIRECTIVES, typeDefs],
    introspection: environment.apollo.introspection,
    mockEntireSchema: false,
    mocks: {
      DateTime: DateTimeMock,
      EmailAddress: EmailAddressMock,
    },
    playground: environment.apollo.playground,
  });

  server
    .listen(environment.port)
    .then(({ url }) => console.log(`Server ready at ${url}. `));

  if (module.hot) {
    module.hot.accept();
    module.hot.dispose(async () => {
      server.stop();
      await mongoDbProvider.closeAsync();
    });
  }
})();
```

*   **第 14 行:**向 GraphQL 模式定义添加自定义指令声明。

## **4。验证插件配置工作正常**

尝试用`NODE_ENV=development npm run build`构建 app，检查生成的 TypeScript 类型定义文件`graphql-codegen-typings.d.ts`。给出了两种 MongoDB 模型类型`PostDbObject`和`UserDbObject`，以及匹配 GraphQL 模式类型的类型，如`Post`、`User`等。

我们已经做好了实现解析器的一切准备。我们开始吧！

# 8.实施解析器

如果你还记得我上一篇文章中的[，Apollo 服务器的](https://medium.com/free-code-camp/build-an-apollo-graphql-server-with-typescript-and-webpack-hot-module-replacement-hmr-3c339d05184f)[解析器函数签名](https://www.apollographql.com/docs/graphql-tools/resolvers/#resolver-function-signature)接受四个位置参数:

```
fieldName(obj, args, context, info) { result }
```

解析器函数并没有什么奇特之处——只是纯粹的函数。换句话说，GraphQL 允许您灵活地定义准备数据的步骤，只要返回的数据类型与 GraphQL 模式类型匹配，这些步骤就被认为是有效的。

## **1。实现** `**publishPost**` **变异解析器**

让我们从`publishPost`突变开始。它需要一个`input`参数，而输入是一个包含 post 的`title`和`content`的对象。如果发布成功，则返回新发布的`post`。

让我们将`publishPost`突变添加到现有的解析器文件中。

文件:`src/resolvers.ts`

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';
import { ObjectID } from 'mongodb';

import { PostDbObject, PublishPostInput } from './graphql-codegen-typings';
import { mongoDbProvider } from './mongodb.provider';

const mockCurrentUserId = '0123456789abcdef01234567';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
  Mutation: {
    publishPost: async (
      obj: any,
      { input }: { input: PublishPostInput }
    ): Promise<PostDbObject> => {
      const result = await mongoDbProvider.postsCollection.insertOne({
        title: input.title,
        content: input.content,
        publishedAt: new Date(),
        author: new ObjectID(mockCurrentUserId),
      });

      return result.ops[0] as PostDbObject;
    },
  },
};
```

*   **第 11 行:**我们使用一个“硬编码”的 ID 来代表一个被模仿的当前用户，只是为了演示的目的。在现实世界的场景中，这可以从一个传入的令牌中获得，例如 [JWT](https://jwt.io) 。
*   **第 18 行:**用上面提到的签名定义一个`publishPost`突变异步函数。
*   **第 20 行:**获取从变异的`args`传来的`input`对象(此处[解释](https://www.apollographql.com/docs/graphql-tools/resolvers/#resolver-function-signature))。TypeScript 类型`PublishPostInput`是由 GraphQL codegen 基于我们的 GraphQL 模式自动生成的。
*   **第 22 行:**我们正在使用一个 helper 方法 form `MongoProvider`来获取 MongoDB 的`posts`集合，并调用集合的`[insertOne()](http://mongodb.github.io/node-mongodb-native/3.3/api/Collection.html#insertOne)`方法向`posts`集合添加一个 post。
*   **第 29 行:**返回新发布的`post`数据库对象，其中包含一个从 MongoDB 分配的 ID(更多细节请参考 MongoDB 的`[insertOneWriteOpResult](http://mongodb.github.io/node-mongodb-native/3.3/api/Collection.html#~insertOneWriteOpResult)` API)。

现在，如果我们试图在 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground) 中运行`publishPost`突变:

```
mutation TestPublishPost($input: PublishPostInput!) {
  publishPost(input:$input) {
    id
    title
    content
    publishedAt
    author {
      id
      firstName
      lastName
      email
    }
  }
}# Query variables. 
{
  "input": {
    "title": "Test 1 Title",
    "content": "Test 1 content. "
  }
}
```

您应该会得到类似如下的结果:

```
{
  "data": {
    "publishPost": {
      "id": "48c3929b-9ddc-4023-a0ed-6dd03e88afc3",
      "title": "Test 1 Title",
      "content": "Test 1 content. ",
      "publishedAt": "2019-12-08T06:15:18.755Z",
      "author": {
        "id": "80f976ae-4d4e-4705-8d6c-f3452e2f8ae0",
        "firstName": "Hello World",
        "lastName": "Hello World",
        "email": "[test@test.com](mailto:test@test.com)"
      }
    }
  }
}
```

发布的帖子的`title`、`content`和`publishedAt`显示正确。然而，作者的用户 ID 没有显示我们模拟的当前用户 ID `0123456789abcdef01234567`,用户的详细信息在模拟值中提供。而且，如果你尝试用 [mongo shell](https://docs.mongodb.com/manual/mongo) (或者其他类似 [Robo 3T](https://robomongo.org) 的工具)查询`posts`集合，你会注意到 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground) 中显示的发布帖子的`ID`与 MongoDB 中的【真实】`ID`并不匹配。

为什么？！

这是因为我们已经在 Apollo 服务器上启用了模拟，将`mockEntireSchema`设置为`false`。这意味着，如果我们的解析器能够完成传入的查询或变异，Apollo 服务器将尝试返回“真实”数据。否则，它将向无法解析的字段返回一个模拟值。这实际上是一种健壮的方法——它允许开发人员基于商定的模式定义，增量地实现解析器。想象一下，当构建一个真实世界的应用程序时，后端团队可以逐步实现和微调解析器，而不会干扰前端团队在 UI 方面的进展。

回到我们的应用程序，我们的`publishPost`变异没有从 MongoDB 返回“真正的”ID 的原因是，模式期望文章的 ID 存储在一个`id`字段中，而 MongoDB 中文档的 ID 表示在一个`_id`字段中。

类似于`Post`对象，我们的 Apollo 服务器目前正在返回模拟的`author`细节，因为我们还没有为`User`类型实现任何解析器。

因此，为了解决`Post` ID 问题，让我们为`Post`类型添加一个解析器。

文件:`src/resolvers.ts`

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';
import { ObjectID } from 'mongodb';

import {
  Post,
  PostDbObject,
  PublishPostInput,
} from './graphql-codegen-typings';
import { mongoDbProvider } from './mongodb.provider';

const mockCurrentUserId = '0123456789abcdef01234567';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
  Mutation: {
    publishPost: async (
      obj: any,
      { input }: { input: PublishPostInput }
    ): Promise<PostDbObject> => {
      const result = await mongoDbProvider.postsCollection.insertOne({
        title: input.title,
        content: input.content,
        publishedAt: new Date(),
        author: new ObjectID(mockCurrentUserId),
      });

      return result.ops[0] as PostDbObject;
    },
  },
  Post: {
    id: (obj: Post | PostDbObject): string =>
      (obj as PostDbObject)._id
        ? (obj as PostDbObject)._id.toString()
        : (obj as Post).id,
  },
};
```

*   第 37 行:为`Post`的`id`字段定义一个解析器。使用[解析器的 obj 参数](https://www.apollographql.com/docs/graphql-tools/resolvers/#resolver-obj-argument)，我们基本上告诉 Apollo 服务器返回当前对象的`id`值(如果存在的话)(即`obj`是一个`Post`类型)。否则，返回当前对象的`_id`字段(即`obj`是`PostDbObject`类型)。这种方法一开始可能会有点混乱。这篇文章可能会帮助你理解这些概念。

类似于帖子 ID，解析帖子的`author`字段:

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';
import { ObjectID } from 'mongodb';

import {
  Post,
  PostDbObject,
  PublishPostInput,
  User,
  UserDbObject,
} from './graphql-codegen-typings';
import { mongoDbProvider } from './mongodb.provider';

const mockCurrentUserId = '0123456789abcdef01234567';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
  Mutation: {
    publishPost: async (
      obj: any,
      { input }: { input: PublishPostInput }
    ): Promise<PostDbObject> => {
      const result = await mongoDbProvider.postsCollection.insertOne({
        title: input.title,
        content: input.content,
        publishedAt: new Date(),
        author: new ObjectID(mockCurrentUserId),
      });

      return result.ops[0] as PostDbObject;
    },
  },
  Post: {
    id: (obj: Post | PostDbObject): string =>
      (obj as PostDbObject)._id
        ? (obj as PostDbObject)._id.toString()
        : (obj as Post).id,
    author: async (obj: Post | PostDbObject): Promise<User | UserDbObject> =>
      obj.author instanceof ObjectID
        ? (mongoDbProvider.usersCollection.findOne({
            _id: obj.author,
          }) as Promise<UserDbObject>)
        : obj.author,
  },
  User: {
    id: (obj: User | UserDbObject): string =>
      (obj as UserDbObject)._id
        ? (obj as UserDbObject)._id.toString()
        : (obj as User).id,
  },
};
```

*   **第 43–48 行:**为 post 的`author`字段定义一个解析器。如果当前对象的`author`字段包含用户 ID，则从`users`集合中检索作者的详细信息。否则，按原样返回当前对象的`author`字段(即`author`是`User`类型)。
*   **第 51–54 行**:类似于文章 ID，我们使用相同的方法来规范化用户的`id`字段。

现在，如果你试图在 GraphQL Playground 中再次运行相同的`publishPost`突变:

```
mutation TestPublishPost($input: PublishPostInput!) {
  publishPost(input:$input) {
    id
    title
    content
    publishedAt
    author {
      id
      firstName
      lastName
      email
    }
  }
}# Query variables. 
{
  "input": {
    "title": "Test 2 Title",
    "content": "Test 2 content. "
  }
}
```

您应该看到结果现在反映了来自 MongoDB 的“真实”数据。

```
{
  "data": {
    "publishPost": {
      "id": "5deca77cd2f17a34441d5332",
      "title": "Test 2 Title",
      "content": "Test 2 content. ",
      "publishedAt": "2019-12-08T07:34:20.298Z",
      "author": {
        "id": "0123456789abcdef01234567",
        "firstName": "Test",
        "lastName": "User 1",
        "email": "[test.user1@test.com](mailto:test.user1@test.com)"
      }
    }
  }
}
```

## **2。实现** `**post**` **查询解析器**

我们已经完成了最困难的部分。实现`post`查询应该相当简单。

文件:`src/resolvers.ts`

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';
import { ObjectID } from 'mongodb';

import {
  Post,
  PostDbObject,
  PublishPostInput,
  User,
  UserDbObject,
} from './graphql-codegen-typings';
import { mongoDbProvider } from './mongodb.provider';

const mockCurrentUserId = '0123456789abcdef01234567';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
  Query: {
    post: (obj: any, { id }: { id: string }): Promise<PostDbObject | null> =>
      mongoDbProvider.postsCollection.findOne({ _id: new ObjectID(id) }),
  },
  Mutation: {
    publishPost: async (
      obj: any,
      { input }: { input: PublishPostInput }
    ): Promise<PostDbObject> => {
      const result = await mongoDbProvider.postsCollection.insertOne({
        title: input.title,
        content: input.content,
        publishedAt: new Date(),
        author: new ObjectID(mockCurrentUserId),
      });

      return result.ops[0] as PostDbObject;
    },
  },
  Post: {
    id: (obj: Post | PostDbObject): string =>
      (obj as PostDbObject)._id
        ? (obj as PostDbObject)._id.toString()
        : (obj as Post).id,
    author: async (obj: Post | PostDbObject): Promise<User | UserDbObject> =>
      obj.author instanceof ObjectID
        ? (mongoDbProvider.usersCollection.findOne({
            _id: obj.author,
          }) as Promise<UserDbObject>)
        : obj.author,
  },
  User: {
    id: (obj: User | UserDbObject): string =>
      (obj as UserDbObject)._id
        ? (obj as UserDbObject)._id.toString()
        : (obj as User).id,
  },
};
```

*   **第 24 行:**使用同样的老技巧，我们首先从 resolver 函数的第二个参数中获得一个 post `id`，并从`posts`集合中找到具有匹配 post ID 的 post。

为了测试`post`查询，在 [GraphQL Playground](https://www.apollographql.com/docs/apollo-server/testing/graphql-playground) 中运行以下查询。使用先前`publishPost`突变的结果中的文章 ID(例如，本例中的`5deca77cd2f17a34441d5332`—您的将会不同)。

```
{
  post(id: "5deca77cd2f17a34441d5332") {
    id
    title
    content
    publishedAt
    author {
      id
      firstName
      lastName
      email
    }
  }
}
```

它应该会返回类似如下的结果:

```
{
  "data": {
    "post": {
      "id": "5deca77cd2f17a34441d5332",
      "title": "Test 1 Title",
      "content": "Test 1 content. ",
      "publishedAt": "2019-12-08T08:08:18.616Z",
      "author": {
        "id": "0123456789abcdef01234567",
        "firstName": "Test",
        "lastName": "User 1",
        "email": "[test.user1@test.com](mailto:test.user1@test.com)"
      }
    }
  }
}
```

# 数据库中不存在的字段怎么办？

到目前为止，我们已经实现了类似于`id`、`author`等字段的解析器，它们在 GraphQL 模式和数据库模型上都有定义。但是，在 GraphQL 模式中定义的、没有存储在数据库中的字段怎么办？(例如用户的`posts`、`followers`字段等。).

记住，GraphQL 并不关心字段的数据是在哪里或者如何形成的。经验法则是:只要返回的字段类型与模式中定义的类型匹配，它们就被认为是有效的。

考虑到这一点，很容易实现用户的`posts`字段。只是用你的魔法来解决这个领域。

文件:`src/resolvers.ts`

```
import {
  DateTimeResolver,
  EmailAddressResolver,
  UnsignedIntResolver,
} from 'graphql-scalars';
import { ObjectID } from 'mongodb';

import {
  Post,
  PostDbObject,
  PublishPostInput,
  User,
  UserDbObject,
} from './graphql-codegen-typings';
import { mongoDbProvider } from './mongodb.provider';

const mockCurrentUserId = '0123456789abcdef01234567';

export const resolvers = {
  DateTime: DateTimeResolver,
  EmailAddress: EmailAddressResolver,
  UnsignedInt: UnsignedIntResolver,
  Query: {
    post: (obj: any, { id }: { id: string }): Promise<PostDbObject | null> =>
      mongoDbProvider.postsCollection.findOne({ _id: new ObjectID(id) }),
  },
  Mutation: {
    publishPost: async (
      obj: any,
      { input }: { input: PublishPostInput }
    ): Promise<PostDbObject> => {
      const result = await mongoDbProvider.postsCollection.insertOne({
        title: input.title,
        content: input.content,
        publishedAt: new Date(),
        author: new ObjectID(mockCurrentUserId),
      });

      return result.ops[0] as PostDbObject;
    },
  },
  Post: {
    id: (obj: Post | PostDbObject): string =>
      (obj as PostDbObject)._id
        ? (obj as PostDbObject)._id.toString()
        : (obj as Post).id,
    author: async (obj: Post | PostDbObject): Promise<User | UserDbObject> =>
      obj.author instanceof ObjectID
        ? (mongoDbProvider.usersCollection.findOne({
            _id: obj.author,
          }) as Promise<UserDbObject>)
        : obj.author,
  },
  User: {
    id: (obj: User | UserDbObject): string =>
      (obj as UserDbObject)._id
        ? (obj as UserDbObject)._id.toString()
        : (obj as User).id,
    posts: (obj: User | UserDbObject): Promise<Post[]> =>
      mongoDbProvider.postsCollection
        .find({
          author: (obj as User).id
            ? new ObjectID((obj as User).id)
            : (obj as UserDbObject)._id,
        })
        .toArray(),
  },
};
```

*   **第 59–66 行:**从解析器的第一个(`obj`)参数中获取“this”用户对象的 ID。从`posts`集合中查找所有字段`author`等于“this”对象的用户 ID 的文章(记住作者在 MongoDB 的`posts`集合中存储为字符串 ID)。

在实现了一些解析器映射之后，您可能会发现解析器模式实际上非常相似。因此，我不会在这篇文章中讨论所有的类型字段——我留下了`followUser`、`unfollowUser`、`likePost`变异解析器以及`Post`和`User`类型中的一些字段作为练习。

# 包扎

自从今年早些时候我发表了上一篇文章以来，GraphQL 生态系统已经真正成熟了。有了各种各样的工具集，开发工作流被大大简化了，用 GraphQL 开发已经成为一种乐趣。

虽然这个应用程序展示了与数据库技术集成的 GraphQL 工具集，但为了保持设置简单，我们将 GraphQL 模式组合在一个文件中，并将解析器组合在一个对象中，这可能很难维护和扩展。

在我的下一篇文章中，我将演示如何模块化一个可扩展的 Apollo 服务器——敬请期待！