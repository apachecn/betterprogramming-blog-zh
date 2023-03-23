# 我为迪纳摩数据库映射器制作了一个 Kotlin 插件

> 原文：<https://betterprogramming.pub/i-made-a-kotlin-plugin-for-dynamo-db-mapper-cce1924fcd1e>

## 豆子不再是你唯一的选择

![](img/3144bc771f1b84f9f61cdccfda2acb86.png)

照片由 [Nana Smirnova](https://unsplash.com/@nananadolgo) 在 [Unsplash](https://unsplash.com/photos/IEiAmhXehwE) 上拍摄

Kotlin 和来自 [aws-sdk-java-v2](https://github.com/aws/aws-sdk-java-v2) 的 Dynamo DB Mapper 并没有真正融合。您可以尝试映射一个 Kotlin 数据类，但是在这个过程中,`BeanTableSchema`会迫使您放弃一些 Kotlin 最好的特性。

*   所有属性都必须是可变的
*   必须有一个无参数的构造函数，这意味着每个属性必须有一个默认值(通常为 null)
*   所有 Dynamo 注释都必须附加到带有前缀`@get`的属性 getters

我通常以这样一个丑陋的烂摊子结束

毛；但是如果你不遵守这些规则，映射器就不能和你的类一起工作。我这样做了很多年:憎恨我被迫做的每一颗豆子；希望有更好的方法。所以我终于做了一个。

# 介绍`Data Class Table Schema`

我很高兴地宣布，我已经建立了一个插件，最终支持 Kotlin 数据类作为一等公民！你需要做的就是用`DataClassTableSchema`替换`BeanTableSchema`，使用新的注释，然后正常使用 SDK。现在，您可以使用一个完全惯用的数据类作为您的模型:属性不需要可变，不需要零参数构造函数，并且您可以将新的注释直接应用于属性，无需前缀。真是松了一口气！

一个看起来像数据类的数据类！

# 看看吧！

开始使用插件，停止讨厌你的数据库模型。尽情享受吧！

[](https://github.com/oharaandrew314/dynamodb-kotlin-module) [## GitHub-oharaandrew 314/dynamo db-kotlin-Module:dynamo db 增强的 SDk 的 kot Lin 模块

### v2 dynamodb 增强型 SDK 的 Kotlin 模块。

github.com](https://github.com/oharaandrew314/dynamodb-kotlin-module)