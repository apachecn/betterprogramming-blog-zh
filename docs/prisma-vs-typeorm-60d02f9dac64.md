# Prisma vs. TypeORM

> 原文：<https://betterprogramming.pub/prisma-vs-typeorm-60d02f9dac64>

## Node.js/TypeScript 生态系统中数据库工具的实际应用

![](img/587f95f164d72be1aa9ef2ff289b4809.png)

托拜厄斯·菲舍尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

使用数据库是后端开发人员不可避免的一部分。

特别是，服务器端 JavaScript 生态系统提供了无数的库，旨在减轻这项任务的痛苦。一些最受欢迎的包括:

*   [**Sequelize**](https://www.npmjs.com/package/sequelize)**(一个对象关系映射器，或 ORM，库)**
*   **[**pg**](https://www.npmjs.com/package/pg)**/**[**MySQL**](https://www.npmjs.com/package/mysql)**(用于与特定数据库接口的 JavaScript 客户端)****
*   ****[**Knex.js**](https://www.npmjs.com/package/knex) (通用查询生成器)****

****这些库各有优缺点。****

****使用像 pg 这样的低级库可以提供显著的性能优势，但代价是维护起来很困难(当您编写原始 SQL 查询时)。尽管如此，使用这些库可能会让新开发人员感到困惑，因为他们需要对 SQL 有很强的理解。****

****另一方面，像 Sequelize 这样的 ORM 提供了方便的抽象，通常简化了数据库的工作。然而，它们是以性能为代价的，因为生成的底层 SQL 通常不如有经验的程序员编写的 SQL 高效。****

****Knex.js 这样的查询构建器旨在比 ORM 更简单、更高效，但比原始 SQL 更易于使用和维护。尽管有这个目标，他们仍然需要很好地理解 SQL，以便在大多数情况下使用。****

> ****“任何傻瓜都能写出计算机能理解的代码。优秀的程序员会写出人类能理解的代码。”—马丁·福勒****

****总的来说，没有完美的工具来处理数据库。然而，TypeScript 和 Node.js 的日益流行导致了几个旨在提供改进的开发人员 UX、简化的代码和可接受的性能的库的发展。其中两个是 [Prisma](https://www.prisma.io/) 和 [TypeORM](https://typeorm.io/#/) 。****

****在接下来的几节中，我们将看一下每个工具的高级概述，浏览一些常见的开发人员场景，并从开发人员的角度对每个场景进行评估。****

# ****大局****

## ****Prisma 概述****

******注意:**在本文中，我们将关注 Prisma 2.x。虽然 Prisma 与 JavaScript 兼容，但以下所有示例都将使用 TypeScript。****

****乍一看，最常见的问题之一是，“*Prisma 是 ORM 吗？*“虽然它与 ORM 有一些共同的特征，但深入观察就会发现它是不同的。****

> ****ORM 是将数据库中的表映射到编程语言中的类的库。另一方面，Prisma 是一个数据库工具包。该工具包包括 Prisma Client，这是一个自动生成的查询生成器，可以显示为您的模型定制的查询*。所有 Prisma 客户端查询都返回普通的旧 JavaScript 对象。— [Prisma 文档](https://www.prisma.io/docs/understand-prisma/prisma-in-your-stack/is-prisma-an-orm)*****

****分解上述引文，Prisma 将自己标榜为“数据库工具包”其中的三个主要组成部分是:****

*   ****[**Prisma 客户端**](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client) (生成应用程序代码中使用的客户端)****
*   ****[**Prisma Migrate**](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-migrate)**(允许你改变数据库模式)******
*   ******[**Prisma 工作室**](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-studio) (一个可视化的数据库编辑器)******

****在写这篇文章的时候，Prisma Migrate 和 Prisma Studio 还处于实验阶段。有关开发时间表的详细信息，请参见产品路线图[此处](https://www.notion.so/Prisma-public-roadmap-50766227b779464ab98899accb98295f)。****

****Prisma 和 ORM 的一个显著区别是它的集中式模式文件和模式语言。Prisma 将数据库模型从代码中分离出来，而不是在应用程序中将类映射到数据库实体。****

****此外，Prisma CLI 使用此模式文件自动生成一个客户端，该客户端可在您的 TypeScript 代码中使用，以便与数据库进行交互。这种范式与 ORM 略有不同，在 ORM 中，模型类的实例扮演这个角色。****

## ****类型概述****

******注意:**演示 TypeORM 的代码示例将使用存储库、查询构建器和 TypeScript(尽管支持普通的 JavaScript)。****

****TypeORM 是 Node.js 的开源 ORM，它利用了现代 JavaScript 特性，并鼓励使用 TypeScript。看它的语法和结构，很明显它从其他编程语言/框架中的成功 ORM 中获得了灵感，比如[实体框架](https://docs.microsoft.com/en-us/aspnet/entity-framework)(。NET)和 [Hibernate](https://docs.jboss.org/hibernate/orm/5.4/javadocs/) (Java)。****

****从较高的层面来看，TypeORM 包含了一些特性，比如迁移、事务、CLI 以及与应用程序数据模型中定义的实体进行交互的多种方式。实体是 TypeORM 的核心组件之一，和其他 ORM 一样，它们是映射到数据库表的类。****

****TypeORM 更强大的特性之一是如何在代码中处理实体。具体来说，TypeORM 支持[活动记录](https://en.wikipedia.org/wiki/Active_record_pattern)和[数据映射器](https://en.wikipedia.org/wiki/Data_mapper_pattern)模式。****

****在高层次上，活动记录模式涉及数据库功能(创建、读取、更新、删除等。)直接在模型类上定义。例如，如果我们有一个 company 类，那么每个类实例都在它的方法集中包含像`create`这样的数据库函数。同样，TypeORM 将此实现为在任何扩展内置`[BaseEntit](https://typeorm.delightful.studio/classes/_repository_baseentity_.baseentity.html)y`类的实体类上定义的数据库操作。****

****现在看看数据映射器模式，它与活动记录模式有细微的不同。特别是，数据映射器模式包含了与定义数据库功能的实体相分离的类(有时称为[数据访问对象](https://en.wikipedia.org/wiki/Data_access_object))。在这种范式中，实体是定义其属性的简单数据结构，而数据映射器类公开了与数据库交互所需的实际功能。TypeORM 提供了两个抽象，允许您应用这个模式，[存储库](https://typeorm.io/#/working-with-repository)和[实体管理器](https://typeorm.io/#/working-with-entity-manager)。这两者之间的根本区别在于，存储库只对单一实体类型进行操作，也就是说，一个`Company`实体对应一个`CompanyRepository`。相比之下，实体管理器是所有存储库的集合。****

****TypeORM 提供了另一种处理数据库的方法，称为[查询构建器](https://typeorm.io/#/select-query-builder)。这个特性将 Knex.js 之类的查询构建库集成到 TypeORM 中。它对于复杂的查询特别有用，使用 SQL 比其他类型的抽象(存储库、实体管理器等)公开的 API 更简单。).我们将在后面的章节中研究如何有效地应用这个工具。****

****概述结束后，让我们通过基于代码的示例深入一些使用每种技术的常见开发人员场景。****

******注意**:以下示例的完整/工作源代码可以在[这里](https://github.com/nmchenry01/prisma-typeorm-blog)找到。****

# ****设置 Prisma****

****要设置 Prisma，我们需要一些依赖项。具体来说， [Prisma 客户端](https://www.npmjs.com/package/@prisma/client)和 [Prisma CLI](https://www.npmjs.com/package/@prisma/cli) 。****

****一旦这些都在我们的`package.json`中，我们还需要一个 Prisma 模式文件，如下所示:****

****Prisma 模式文件示例****

****这里我们看到了几个 Prisma 构造，包括生成器、数据源和模型。****

****生成器块提供了我们运行 CLI `prisma generate`命令时将创建的客户端的详细信息。在这个特殊的例子中，我们指定我们想要创建一个 JavaScript 客户机。值得注意的是，Prisma 还支持一个 [Go](https://github.com/prisma/prisma-client-lib-go) 客户端。****

****数据源块定义了数据库连接。在上面的实例中，我们使用由下面所示的`.env`文件提供的 URL 连接到本地 Postgres 实例:****

****答。定义 URL 的 env 文件****

****模型块表示作为应用程序域一部分的表。我们有三张桌子，包括`Company`、`Product`和`Customer`。使用 Prisma 模式语言(PSL)，我们可以简洁地定义表之间的关系([一对多](https://en.wikipedia.org/wiki/One-to-many_(data_model))、[多对多](https://en.wikipedia.org/wiki/Many-to-many_(data_model))等)。)、主键、唯一性约束等等。****

****在产品和客户之间定义多对多关系的情况下，我们不需要在模式中显式声明一个连接表。为此，我们使用 Prisma 的[隐式多对多](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-schema/relations#implicit-many-to-many-relations)关系特性。这个特性仍然在底层数据库中创建一个连接表，但是允许我们从代码中抽象出来。****

****为了获得更多的细节和更深入地了解 PSL 的可能性，我鼓励你查看一下官方规格。****

****最后，为了在我们的应用程序代码中使用 Prisma 客户机，我们需要为我们的数据模型生成一个客户机，并应用数据库模式。我们通过 CLI 运行`prisma generate`和`prisma migrate save --experimental && prisma migrate up --experimental`来实现这一点。****

****如果一切顺利，我们的应用程序的模式将被应用到本地数据库，并且在我们的`node_modules/@prisma/client`目录中会生成一个定制的 Prisma 客户端。****

# ****设置类型表单****

****要安装 TypeORM，我们还需要安装一些依赖项。值得注意的是，我们需要[类型的表单包](https://www.npmjs.com/package/typeorm)、[反射元数据](https://www.npmjs.com/package/reflect-metadata)，以及一个数据库驱动程序(在我们的例子中，是 [pg](https://www.npmjs.com/package/pg) )。****

****一旦这些都在我们的`package.json`中，我们将需要一个如下所示的配置文件:****

****TypeORM 的配置文件****

****在配置文件中，我们做了各种事情，包括:****

*   ****为我们的本地 Postgres 数据库定义连接细节****
*   ****将`synchronize`选项设置为 true，这样无论何时连接，我们的应用程序和数据库之间的数据模型都是一致的(在生产中应该是关闭的)****
*   ****在我们的实体被传送后指向它们的位置****

****值得注意的是，TypeORM 支持 JSON 以外的配置文件格式。更多详细信息，请参见此处的。****

****最后一步是定义属于应用程序域的实体。我们将使用与 Prisma 安装时相同的工具:****

****公司实体****

****产品实体****

****客户实体****

****在上面的模型中，我们创建了与前面相同的列、关系和约束。为了定义所有这些，TypeORM 利用了 TypeScript[decorator](https://www.typescriptlang.org/docs/handbook/decorators.html)。这样做的结果是一个清晰的语法，易于阅读和理解。关于 TypeORM 装饰器的完整列表，请看这里的。****

# ****创造****

## ****普里斯马****

****首先，我们将在下面插入一家公司:****

****创建公司(Prisma)****

****熟悉 GraphQL 的开发人员会很快熟悉 Prisma 的语法。在上面的代码中，我们使用了`create`函数来插入一个新的数据库记录。我们通过在`data`字段中提供创建新公司所需的字段来实现这一点(只有`name`属性是必需的*)。*****

****顺便提一下，Prisma 的一个很酷的特性是它还可以生成健壮的类型脚本类型:****

****公司实体的自动生成类型之一****

****这样做的结果是一个直观的开发人员 UX，使它直截了当地理解我们的自定义 Prisma 客户端的 API。****

****接下来，让我们看看涉及关系的更复杂的用例:****

****创建产品并将其链接到公司(Prisma)****

****创建一个客户并将其链接到多个产品(Prisma)****

****在上面，我们做了几件事，包括:****

*   ****在我们的数据库中创建了新产品和客户****
*   ****通过公司的`name`领域将新产品与现有公司联系起来(多对一/一对多关系)****
*   ****通过他们的`name`字段将一个新客户链接到几个现有产品(多对多关系)****

****Prisma 提供的另一个很酷的特性是为有关系的实体执行[嵌套写](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/relation-queries#nested-writes)的能力。这意味着能够一次创建多个相关实体，同时作为一个事务进行操作:****

****嵌套写入的示例(Prisma)****

## ****字体****

****现在让我们做同样的一系列创建操作，但是使用 TypeORM。同样，我们将创建一个具有相同属性的公司:****

****创建公司(类型)****

****使用存储库模式，我们检索特定于我们的数据库模型之一的存储库，并使用它通过`create`方法创建该模型的实例。然后我们通过调用`save`将它持久化到数据库中。****

****现在让我们看一些使用产品和客户实体的更复杂的创建示例:****

****创建产品并将其链接到公司(TypeORM)****

****在创造新产品的情况下，我们需要`ProductRepository`和`CompanyRepository`。****

****`CompanyRepository`用于在数据库中查找与产品相关的公司。然后使用`ProductRepository`创建一个新产品，包括它与公司实体的外键关系。****

****我们再次使用`save`操作将产品持久化到数据库中。****

****创建一个客户并将其链接到几个产品(TypeORM)****

****与前面的例子类似，我们使用多个存储库来创建一个新客户。在上面的例子中，我们找到一个客户消费的所有产品，创建一个新客户(包括外键)，最后保存这个新客户。****

# ****阅读****

## ****普里斯马****

****从一个简单的例子开始，按名称查找单个公司如下所示:****

****按名称查找公司(Prisma)****

****考虑到我们已经看到的语法，这应该是有意义的，并使用一个过滤条件通过特定字段来查找公司。****

****现在来看一个更复杂的例子。例如，如果我们想找到所有消费特定公司产品的客户，该怎么办？****

****查找公司客户的用户名(Prisma)****

****在上面，我们利用[字段选择](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/field-selection)和[排序](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/sorting)。具体来说，我们只按降序返回相关客户的`username`字段。****

****另外，`some`操作符的使用表明我们正在寻找一个或多个记录与所提供的标准相匹配的产品(至少有一个产品需要与名为“Acme”的公司相关)。****

****这个查询也可以从关系的另一端实现(从`Company`开始查询，并使用关系的另一端)。****

****最后一个例子，Prisma 也支持使用[多重过滤条件](https://gist.github.com/nmchenry01/7000b1048191123aab11d2d39afd8df1):****

****寻找制造汽车或炸药的公司(Prisma)****

## ****字体****

****我们将从按名称查找公司开始:****

****按名称查找公司(类型)****

****在上面的例子中，我们使用了`CompanyRepository`和它的`findOne`方法来基于一个条件(在这个例子中是`name`属性)检索一个公司。****

****看下一个案例，让我们找到一个特定公司的所有客户:****

****查找公司客户的用户名(TypeORM)****

****这是 TypeORM 变得更有趣的地方。****

****我们试图在上面的查询中执行不仅仅是基本的查找操作。鉴于此，利用 TypeORM 的查询构建器是有意义的(因为使用存储库 API 执行相同的操作很麻烦)。****

****如前所述，查询构建器是一个 TypeORM 特性，允许您使用直观的 API 构建和执行 SQL 查询。****

****使用`CustomerRepository`，我们首先为客户表创建一个查询构建器实例。然后，我们执行连接操作，选择与我们的客户消费的产品相关的所有公司，然后过滤出符合特定条件的公司(company `name`属性)。最后，我们只选择用户名，并按降序排列输出。****

****作为查询构建器的另一个应用，下面是一个示例，说明如何找到生产一组特定产品的所有公司:****

****寻找制造汽车或炸药的公司****

# ****更新****

## ****普里斯马****

****用 Prisma 更新数据库记录使用的语法类似于 create 和 read 示例。更新产品描述如下所示:****

****更新产品描述(Prisma)****

****这里我们在`where`字段(`name`属性)中提供一个搜索条件，然后在`data`属性(`description`属性)中包含要更新的字段。****

## ****字体****

****使用与上面相同的示例，更新 TypeORM 中的产品描述如下所示。****

****更新产品描述(类型)****

****由于这是一个相对简单的查询，我们在`ProductRepository`上使用了`update`函数。这个函数有两个对象，第一个是搜索条件，第二个是要更新的字段/值。****

# ****删除****

## ****普里斯马****

****我们将在 Prisma 中看到的下一个常见开发人员场景是删除数据库条目。例如，按特定筛选条件删除客户如下所示:****

****按用户名删除客户(Prisma)****

****`delete`操作支持与上一节所示的`findOne`和`findMany`操作相同的过滤能力。****

****另外，`delete`操作只删除一个数据库条目。对于批量删除，使用`deleteMany`。****

## ****字体****

****按用户名删除客户如下所示:****

****按用户名删除客户(类型)****

****`CustomerRepository`上的`delete`操作接受一个带有搜索条件的对象作为参数，可用于删除一个或多个数据库条目。****

# ****处理****

## ****普里斯马****

****我们已经展示了一种使用 Prisma 执行事务的方法。具体来说，无论何时创建相关实体作为[嵌套写](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/relation-queries#nested-writes)的一部分，都有一个事务保证(意味着操作作为一个单元成功或失败)。****

****但是，还有一种执行事务的方法。它使用实验性的[事务 API](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-client/transactions#bulk-operations-experimental) ，如图所示:****

****交易 API (Prisma)****

****在上面的例子中，我们创建了一个公司和客户作为同一个事务的一部分。当不相关的实体需要事务保证时，这是很有用的，尽管在撰写本文时这个特性还没有准备好投入生产。****

## ****字体****

****TypeORM 还提供了一个用于执行事务的 API，如下所示:****

****事务 API(类型)****

****TypeORM 中的事务依赖于实体管理器上的`transaction`方法。反过来，该方法将一个函数作为参数，该参数提供一个新的实体管理器，该管理器必须用于事务中的所有操作。****

****在上面的示例中，我们使用特定于事务的实体管理器创建了一个公司和用户。****

# ****原始 SQL****

## ****普里斯马****

****Prisma 提供了使用原始 SQL 执行查询的能力，如下所示:****

****原始 SQL (Prisma)****

****这种方法的一个缺点是，您失去了 Prisma 客户端自动生成的类型的类型安全性。尽管如此，编写 SQL 查询有时是必要的，您可以编写自定义类型定义来恢复类型安全。****

## ****字体****

****同样，TypeORM 可以执行原始 SQL 查询:****

****原始 SQL(类型)****

****在很大程度上，查询构建器特性可以用来避免编写原始 SQL(尽管有这个选项还是很方便的)。****

# ****估价****

## ****普里斯马****

****与 Prisma 合作后，我的印象是它有很大的潜力。从开发人员的角度来看，使用它非常简单，尤其是与其他流行的数据库工具如 [Sequelize](https://www.npmjs.com/package/sequelize) 相比。****

****此外，Prisma [文档](https://www.prisma.io/docs/)使得快速建立并运行一个例子变得不那么痛苦。我在使用它时遇到的唯一问题是使用隐式的多对多特性。具体来说，需要多次尝试才能使表命名约定正确，并使用 Prisma CLI 的[自省](https://www.prisma.io/docs/reference/tools-and-interfaces/introspection)功能(这会将现有的数据库模式转换为 Prisma 模式文件)。****

****虽然此功能的限制和要求已被[很好地记录下来](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-schema/relations#conventions-for-relation-tables-in-implicit-m-n-relations)，但这可能很难在生产环境中使用，需要一种变通方法。****

****虽然 Prisma 还有很长的路要走，但它已经取得了巨大的进步，并有可能成为 Node.js 和 TypeScript 的首选数据库工具。****

******优点******

*   ****易于使用和直观的语法****
*   ****在利用 GraphQL 的堆栈中表现出色****
*   ****惊人的类型脚本支持****
*   ****Prisma 模式语言(PSL)既简单又强大****
*   ****由专门的开发人员团队和风险投资资金支持****

******缺点******

*   ****还有一段路要走，许多功能仍在开发中****
*   ****迁移功能( [Prisma Migrate](https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-migrate) )仍处于试验阶段****
*   ****没有长时间运行的事务****
*   ****无法访问底层数据库连接****
*   ****隐含的多对多关系特性有点脆弱****

## ****字体****

****在很多项目中使用过 TypeORM 之后，我一直发现它是一个强大的数据库工具，涵盖了日常用例。值得注意的是，对于用其他 ORM 很难实现的更困难的查询，查询构建器总是很方便。****

****此外，TypeORM 非常有效地使用了 TypeScript 装饰器。这样做的结果是实体类更具表现力，更易于阅读。****

****TypeORM 也继续在开发者中流行，在撰写本文时，该软件包在 NPM 上每周下载量超过了[35 万次。然而，TypeORM 的主要缺点是它在 Github 上有许多未解决的问题和同样多的已知 bug。](https://www.npmjs.com/package/typeorm)****

****由于该项目没有像 Prisma 这样的全职开发人员，支持和修复已知问题的速度相对较慢。随着时间的推移，这可能会导致 TypeORM 的受欢迎程度下降，并限制企业的采用。****

******优点******

*   ****适用于 TypeScript 和 JavaScript****
*   ****大量的特性，并支持多种关系数据库****
*   ****查询生成器功能增加了灵活性和功能****
*   ****有效地使用装饰者****
*   ****迁移功能运行良好/比 Prisma 更成熟****

******缺点******

*   ****大量的 bug 和未解决的问题****
*   ****[文档](https://typeorm.io/#/)不适合高级用例****
*   ****来自 [Github](https://github.com/typeorm/typeorm) 维护者的部分支持****
*   ****不够成熟/支持不够好，不适合企业采用****
*   ****没有大量资金支持****

# ****结束语****

****TypeORM 和 Prisma 都提供了优秀的开发者 UX 和特性集。从短期来看，我预计 TypeORM 会比 Prisma 更受欢迎。这是因为 TypeORM 已经在受欢迎程度上[明显领先于 Prisma，并且在成熟度方面也有优势(例如，迁移)。](https://www.npmtrends.com/@prisma/cli-vs-typeorm)****

****从长远来看，Prisma 很有可能超越 TypeORM，因为它有强大的资金支持和全职核心开发人员团队致力于开发新功能。此外，TypeORM 可能会受到 Github 公开问题/错误和缺乏全职维护人员支持的拖累。****

****不管结果如何，这两种工具都提供了巨大的希望，看到它们如何随着时间的推移而发展将是令人兴奋的。****

****一如既往，我希望听到任何关于 Prisma/TypeORM 的想法，它们在 Node.js 生态系统中的位置，以及真实世界的用例！下次见，感谢阅读。****

# ****参考****

*   ****[文章源代码](https://github.com/nmchenry01/prisma-typeorm-blog)****
*   ****[Prisma 文档](https://www.prisma.io/docs/)****
*   ****[字体文件](https://typeorm.io/#/)****