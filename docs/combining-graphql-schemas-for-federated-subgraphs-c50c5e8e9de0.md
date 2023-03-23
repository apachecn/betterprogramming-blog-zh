# 组合联邦子图的 GraphQL 模式

> 原文：<https://betterprogramming.pub/combining-graphql-schemas-for-federated-subgraphs-c50c5e8e9de0>

## 将多个模式文件转换成一个

![](img/36fabccf2511628a305b3a8f7b373891.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

*TL；DR —* `*@graphql-tools/utils*` *导出* `*printSchemaWithDirectives*` *函数将您的* `*GraphQLSchema*` *对象转换回模式字符串。*

在计划将一个 monolith GraphQL 服务器迁移到 Apollo Federation 时，出现了一个有趣的难题。我们的模式被分割成多个文件，以减轻六个团队在同一个模式上工作的痛苦，我们使用`# import`语法将它们连接在一起。

在这篇简短的博客中，我将解释我们如何能够将这些多个文件合并成一个文件，然后可以用它来创建一个联邦网关的起始子图。

# 模式文件

出于本文的目的，我已经创建了 4 个文件来表示分布在多个`.graphql`文件中的模式。

```
// schema.graphl# import Post from "posts.graphql"type Query {
  posts: [Post]
}// posts.graphql# import Comment from 'comments.graphql'"""
A post with comments
"""
type Post {
  comments: [Comment]
  id: ID!
  text: String!
  tags: [String]
}// comments.graphql# import uppercase from './directives.graphql'type Comment {
  id: ID!
  text: String! [@uppercase](http://twitter.com/uppercase)
}// directives.graphqldirective @uppercase on FIELD_DEFINITION
```

这些文件展示了各种常见的模式语法，包括嵌套类型(即`Comment`)、注释(即`Post`)和指令(即`@uppercase`)

# 加载模式

graphql-tools 为我们提供了将所有这些模式文件加载到单个 GraphQLSchema 对象中所需的函数，然后我们可以`print`取出该对象。如果您继续进行，在您编写模式文件的同一个目录中创建一个新的节点包；

```
$ npm init -y
$ npm i -D graphql @graphql-tools/graphql-file-loader @graphql-tools/load
```

可以用这个简短的脚本将模式文件加载到单个对象中

```
const { loadSchemaSync } = require("[@graphql](http://twitter.com/graphql)-tools/load");
const { GraphQLFileLoader } = require("[@graphql](http://twitter.com/graphql)-tools/graphql-file-loader");const schema = loadSchemaSync("./schema.graphql", {
  loaders: [new GraphQLFileLoader()],
});
```

`# import`关键字告诉`loadSchemaSync`函数在哪里可以找到所需的类型，只要您的模式文件中没有任何语法错误，这个函数就会正确执行。

# 打印模式

`graphql-tools/utils`公开了另一个有用的函数`printSchemaWithDirectives`，它构建在`graphql.js`包的`printSchema`函数之上。使用这个，我们可以添加到我们之前的脚本中，最终将打印的模式写入到一个`combined.graphql`模式中。

```
const fs = require("fs");const { loadSchemaSync } = require("[@graphql](http://twitter.com/graphql)-tools/load");
const { GraphQLFileLoader } = require("[@graphql](http://twitter.com/graphql)-tools/graphql-file-loader");
const { printSchemaWithDirectives } = require("[@graphql](http://twitter.com/graphql)-tools/utils");const schema = loadSchemaSync("./schemas/schema.graphql", {
  loaders: [new GraphQLFileLoader()],
});fs.writeFileSync("combined.graphql", printSchemaWithDirectives(schema));
```

结果文件如下所示；

```
// combined.graphqlschema {
  query: Query
}directive [@uppercase](http://twitter.com/uppercase) on FIELD_DEFINITIONtype Query {
  posts: [Post]
}"""A post with comments"""
type Post {
  comments: [Comment]
  id: ID!
  text: String!
  tags: [String]
}type Comment {
  id: ID!
  text: String! [@uppercase](http://twitter.com/uppercase)
}
```

# 结论

我们已经将多个模式文件转换成一个，现在可以通过`[rover](https://www.apollographql.com/docs/rover/)` CLI 工具运行它来生成我们的超图模式。

对于像我们这样的人来说，这可能是联合一个整体图的第一步，希望这种从 GraphQLSchema 对象重新创建模式文件的能力在这方面对您有用。