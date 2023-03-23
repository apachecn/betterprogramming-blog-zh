# 构建您自己的基于 Keystone.js 的电子商务系统—访问控制

> 原文：<https://betterprogramming.pub/building-your-own-e-commerce-keystone-js-based-system-access-control-1a366ed7e064>

## 改进我们基于电子商务的系统

![](img/60b3fb890428c9edd4f6636f6eb60f4a.png)

照片由 [Nathália Rosa](https://unsplash.com/@nathaliarosa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 介绍

是时候继续我们关于创建基于 Keystone.js 的电子商务系统的系列文章了。以前，我们专注于创建所有的基本模型和环境设置。这次我们将研究访问控制和用户权限。老实说，并不是所有用户都应该被允许访问所有数据并有能力修改它。这是一个严重的安全风险，所以这一次我们要修复它。此外，本文的完成代码可以在我的 [GitHub](https://github.com/eabald/keystone-e-commerce/tree/Access-control) 上获得。

# 要求

那么，我们在这里的要求是什么？首先，我们需要根据用户在系统中的角色来限制用户的访问。基本上，客户只能修改与他们相关的数据，但是管理员有更多的特权。所有这些应该在三个层次上影响系统:首先，限制对 Admin UI 的访问，然后根据每个模式的操作类型过滤访问，最后是模式中某些字段的层次。

# 访问控制实现

首先，让我们更新系统中的用户角色。在上一篇文章中，我们从其中两个开始— `admin`和`customer`。基本的假设是，我们需要两个不同的安全级别和用户特权，但是经过一些考虑，我决定我们需要另一个。所以是时候更新我们的`roles.enum.ts`和`roles.const.ts`并添加`employee`角色了。目前，`admin`和`employee`的特权将保持不变，但随着系统的进一步开发，它可能会改变，如下所示:

这样一来，我们就可以专注于管理 UI 访问了。当然，所有的管理员和员工都应该能够使用它；这是必要的。但是客户应该不能访问它。当然，他们需要登录系统并创建会话，但是 Admin UI 包含不能被广泛访问的敏感信息。

开箱即用，Keystone 为这类问题提供了完美的解决方案。为了限制某些用户访问 Admin UI，我们只需更新我们的主配置文件、`keystone.ts`和`ui.isAccessAllowed`选项。它的函数和`context`参数将返回一个布尔值。以前，我们只检查用户是否有有效的会话数据。现在我们必须检查用户是否有有效的会话，以及他们的角色是否不等于`customer`。

仅此而已；只有具有正确角色的用户才能访问管理用户界面。现在，是时候关注每个模式级别的访问控制了。基本上，我们将使用两种类型的限制:第一，每个人都可以查询，但只有内部用户(`admin`和`employee`)可以修改。第二种情况是内部用户拥有完全访问权限，但是`customer`用户只能查询和更新属于该用户的日期。

让我们遍历所有模式，并更新每个模式的`access`属性。首先是`Address`列表:它包含敏感数据，因此只有特定记录的管理员和所有者才能访问它。

在 Keystone.js 的 access API 中，有三级控制([阅读更多](https://keystonejs.com/docs/apis/access-control))、`operation`、`filter`、`item`。第一个，`operation`允许限制在列表上可以做什么样的操作(`query`、`create`、`update`和`delete`)。其次，`filter`允许将特定的 GraphQL 过滤器添加到该列表上执行的每个操作中，并区分其类型(与之前相同，但没有`create`)。最后一点，`item`更多地在数据层面上工作，允许我们检查传递给每个操作的数据，但是它只对突变工作，所以对查询没有影响。

列表配置的这一部分中的所有属性都是函数。第一个和最后一个应该返回布尔值；第二个应该返回 GraphQL 过滤器。

在`operation`级别的`Address`列表中，我们只需要检查用户是否被认证，但是在`filter`级别，我们必须检查用户是`admin`还是`employee`。那么就不需要额外的滤波器。

但是当用户的角色是`customer`时，我们必须创建和添加过滤器，将结果限制在该用户。对于每个模式，这些函数都非常相似，所以我决定将它们移到另一个文件中，以避免不必要的重复。我已经创建了一个`shared`文件夹和里面的`index.ts`。此外，我为我们的新方法创建了一个名为`filter-customer-access.ts`的文件，并将其添加到`index.ts`的导出中。

基本上，它创建并返回新的 GraphQL 过滤器，检查我们试图访问的记录中的用户 ID 是否等于当前会话中存储的 ID。此外，我添加了一个接口来描述会话参数，主要是因为在核心 Keystone 文件中，会话有一个不太方便的类型`any`。

我们将使用此方法将此过滤器添加到`query`、`update`和`delete`操作中，而不是`create`。第一，不可能，需要另辟蹊径。在这种情况下，我们将对等级限制进行乌拉尔化。这里使用的函数也将被重用，所以我在`shared`文件夹中创建了另一个名为`filter-customer-access-create.ts`的文件。

它看起来与前一个非常相似，但是有两个主要的区别:首先，它返回一个布尔值，而不是`filter`，其次，它检查被传递来创建变异的数据是否有关于用户的信息，以及它是否是当前会话所属的同一个用户。另外，我已经在`index.ts`中为这个方法添加了导出。现在是时候将它添加到`Address`模式配置文件及其`access`部分了，如下所示:

`Order`、`Payment`、`Shipment`有完全相同的限制，所以我也更新了他们的模式。此外，在`Order`模式中，我已经更新了`user`和`employee`字段。以前，他们可以选择从列表中创建一个新的实体，但这并不是他们真正想要的。因此，我在该字段的配置中添加了:

```
ui: {
 hideCreate: true,
},
``
```

除了一些例外，模式限制非常相似。首先，在`item`级别上没有检查。相反，我添加了另一个配置选项— `graphql`。它包含直接应用于 GraphQL 模式的选项，并允许禁用某些操作，在我们的例子中是`create`。

因此，无法通过 GraphQL API 创建新的`Cart`实体，而这正是我们想要的。用户的购物车只能在注册过程中创建一次。稍后会详细介绍。我还更新了一些字段，以阻止从该列表中创建新用户和新产品的可能性。最后一项更改是将默认值添加到`sum`字段。下面是整个更新的模式:

下一组列表有更简单的限制:所有用户都可以查询它们(也没有经过身份验证)，但是只有内部用户可以创建/修改它们。该组包含`Catgegory`、`ProductImage`、`Product`和`Stock`。为了应用这些限制，我们必须在`operation`级别添加访问规则:

在`Stock`模式中，我还添加了一个额外的字段级访问规则，确切地说是`amountInNextDelivery`。关于股票交付的信息可能不是严格保密或敏感的，但竞争正在观察，所以最好为认证用户保留它。

```
amountInNextDelivery: integer({
  access: { read: ({ session }) => !!session },
}),
```

最后更新的模式是`User`；大多数变化与`Address`列表非常相似，但也有一些例外。首先，在`item`层面上，我们没有限制`create`操作。如果我们这样做，没有人能够注册。

相反，我给`update`操作添加了同样的限制。此外，我在这里使用了`hooks`，确切地说是`afterOperation`。我们将在本系列的后续部分中更多地讨论钩子，但是现在，我们所要知道的是，它们允许您执行与当前列表上执行的操作相关的副作用。

我们的钩子首先检查当前操作的类型；如果不是`create`操作，则返回`undefined`。但是在`create`的例子中，我们想继续为新用户创建一个相应的`Cart`实体。但是有一个问题，之前在更新`Cart`模式时，我禁用了负责新购物车创建的 GraphQL API，所以我们必须使用`prisma` API(关于那个的更多信息[)。它允许我们跳过这个限制，而没有允许用户独立创建购物车的风险。下面是更新后的`User`模式:](https://keystonejs.com/docs/apis/context#database-access)

这部分还有一件事要做。我们需要一些测试数据——本例中是用户。Keystone 中包含的 Prisma.js 有一个很好的导入初始数据的方法( [more](https://www.prisma.io/docs/guides/database/seed-database) )，所以让我们使用这个特性。从安装`ts-node`包开始，如`devDependency`所示:

```
yarn add -D ts-node
```

同时，创建文件夹`seed-data`，以及里面的两个文件:`data.json`和`index.ts`。第一个将是我们测试数据的来源；第二个将导入数据。但是另外，我们必须对我们的`tsconfig.josn`做一些小的修改，以便能够直接从 JSON 文件导入数据。因此，在编译器选项下，添加:

```
“resolveJsonModule”: true
```

现在，让我们添加测试数据:

这里使用的密码是每个用户的`1234ABcd@@`。接下来，我们要准备导入逻辑，所以让我们更新`index.ts`:

这个导入器有两个主要部分:首先，我们创建一个负责将数据导入数据库的`main`方法，第二部分以一种安全的方式调用这个函数，具有适当的错误捕获和在任务完成后断开数据库连接的能力。

基本的导入器逻辑非常简单；它从导入的 JSON 加载用户数据，然后遍历它。对于该循环的每次迭代，它检查数据库中是否存在该用户；如果没有，它就创建它。完成之后，这里我们需要的最后一件事就是开始导入的方法。因此，让我们更新我们的`package.json`并添加:

```
"prisma": {
  "seed": "ts-node seed-data"
},
```

此外，我们需要创建一个脚本来使用`yarn`启动所有这些操作，因此在脚本下添加:

```
"seed": "keystone prisma db seed"
```

完成这些后，我们就快到达这一部分的终点了。我们只需要为已经完成的更新创建迁移，并导入测试数据。因此，让我们首先用以下命令启动我们的数据库:

```
docker-compose -f docker-compose.dev.yml up database
```

然后，在容器外启动我们的后端应用程序。所以在`backend`文件夹中运行:

```
yarn dev
```

该脚本将检测数据库模式中的变化，并为我们创建一个迁移。我们只需要为它指定一个名称。比如`new_user_role_and_cart_default_values`。之后，我们可以停止这个脚本并播种我们的测试数据:

```
yarn seed
```

完成后，我们可以重新启动整个系统，并用以下命令进行测试:

```
docker-compose -f docker-compose.dev.yml up
```

# 摘要

这部分到此为止。随着这一变化，我们系统中的数据安全性得到了很大的提高，现在我们可以安全地继续下一个任务，处理每个电子商务系统的核心元素——购物车业务逻辑。

我希望你喜欢它。如果您有任何问题或意见，请随时提问。

祝您愉快！