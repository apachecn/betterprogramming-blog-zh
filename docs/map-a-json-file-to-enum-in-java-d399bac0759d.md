# 用 Java 将 JSON 文件映射到 Enum

> 原文：<https://betterprogramming.pub/map-a-json-file-to-enum-in-java-d399bac0759d>

## 以瑞士奶酪为例的循序渐进教程

![](img/047080cfc336fa3ad4b951f739cb3c79.png)

更多关于法国 AOP 的信息请见[MySwitzerland.com](https://www.myswitzerland.com/en-ch/experiences/food-wine/le-gruyere-aop/)

我最近为一个回访客户启动了一个新的 Java 项目。我的首要任务之一是实现一个新特性，目标是使用 JSON 属性文件使应用程序可全局配置。

我发现由此产生的解决方案相对方便，所以我想我会在一篇新的博客文章中分享它。此外，由于到目前为止我还没有写过任何 Java 博客，我觉得这很有挑战性，也很有趣。

# 介绍

在本文中，我们将:

1.  创建新项目。
2.  读取 JSON 文件和属性。
3.  创建枚举。
4.  将属性映射到泛型枚举。

注意:如果你已经有了一个项目，你显然可以跳过第一章的目标是创建一个项目。同样，如果您不想使用 [Maven](https://maven.apache.org/) ，也可以跳过它，按照您的设置要求包含我们将要使用的库。

# 创建新项目

首先，我们将使用 [Maven](https://maven.apache.org) 初学者工具包创建一个新项目。为此，在终端中运行以下命令:

```
$ mvn archetype:generate -DgroupId=com.jsontoenum.app -DartifactId=json-to-enum -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

如果一切顺利，您现在应该能够正确无误地进入目录来编译项目了:

```
$ cd json-to-enum/ && mvn package
```

# 读取 JSON 文件和属性

起初，我实现了一个快速定制的解决方案，但是我对结果一点也不满意。这就是为什么我试图在网上寻找现成的解决方案，找到了不可思议的开源库，[*com . types safe . config*](https://github.com/lightbend/config)。

它利用了读取 JSON 文件和访问其属性的所有工作，没有依赖性，甚至与 Java 8 兼容。

至少这一次，用谷歌搜索解决方案是个好主意。

为了使用这个库，让我们将这个库的新依赖项添加到我们的`pom.xml`:

```
<dependency>
    <groupId>com.typesafe</groupId>
    <artifactId>config</artifactId>
    <version>1.3.4</version>
</dependency>
```

此外，我们还应该将下面的`<resources/>`和`<plugins/>`目标添加到我们的`<build/>`中，以便能够加载 JSON 文件(我们将要创建的)并将我们刚刚引用的依赖项打包到我们的 JAR 文件中。

```
<resources>
  <resource>
    <directory>src/resources</directory>
  </resource>
</resources>

<plugins>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.2.0</version>
    <executions>
      <execution>
        <phase>package</phase>
        <goals>
          <goal>shade</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
</plugins>
```

我们大楼终于建好了。我们现在可以继续下一步，创建我们的 JSON 属性文件。

默认情况下，配置库会尝试加载一个名为`application.json`的文件作为属性的源。

因此，让我们保持简单，使用预定义的名称在新文件夹`src/resources/`中创建这个新文件，包含以下 JSON 内容:

```
{
  "swiss": {
    "cheese": "gruyere"
  }
}
```

万事俱备，该编码了。

我们可以修改我们的应用程序(文件夹`src/main/java/com/jsontoenum/app`中的`App.java`)来初始化和加载 JSON 文件的属性，并打印出我们最喜欢的奶酪类型的名称。

```
package com.jsontoenum.app;

import com.typesafe.config.Config;
import com.typesafe.config.ConfigFactory;

public class App {
   public static void main(String[] args) {
       // Load and init the configuration library
      final Config conf = ConfigFactory.*load*();

      // Get the value of the JSON property
      final String cheese = conf.getString("swiss.cheese");

      System.*out*.println(String.*format*("I like %s 🧀", cheese));
   }
}
```

让我们编译所有内容，并在终端中使用以下命令行运行我们的项目，以测试我们的实现:

```
$ mvn package && java -cp target/json-to-enum-1.0-SNAPSHOT.jar com.jsontoenum.app.App
```

如果一切正常，您的控制台输出应该在堆栈跟踪的末尾显示以下内容:

```
[INFO] -------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] -------------------------------------------------------------
[INFO] Total time:  2.179 s
[INFO] Finished at: 2019-08-16T15:04:35+02:00
[INFO] -------------------------------------------------------------
I like gruyere 🧀
```

# 创建枚举

即使能够读取 JSON 属性已经很酷了，但在开发我的指定特性时，我很快意识到，如果应用程序应该相应地有不同的行为，那么将属性映射到 enum 可能是强制性的。

此外，瑞士生产不止一种奶酪。

因此，作为我们的下一步，我们可以在文件夹`src/main/java/com/jsontoenum/app/`中创建一个 enum，它可以列出该国法语区的一些奶酪:

```
package com.jsontoenum.app;

public enum Cheese {

   *GRUYERE*,
   *TETE_DE_MOINE*,
   *CHAUX_DABEL*,
   *RACLETTE*,
   *VACHERIN*,
   *TOMME* }
```

# 将属性映射到通用枚举

在本文中，我们使用奶酪作为演示，但显然，瑞士出口许多其他产品，如巧克力和手表。

同样，我正在开发的应用程序不仅仅包含一个枚举。这就是为什么我们不仅添加了一个方法来解析单一类型枚举的属性，而且在我们的应用程序中将其声明为泛型(`App.java`)。

```
private static <E extends Enum<E>> E getEnumProperty(final Config conf, final String key, final Class<E> myClass) {
    // If no config loaded
    if (conf == null) {
        return null;
    }

    // If the config doesn't contains the key
    if (!conf.hasPath(key)) {
        return null;
    }

    // As previously, load the key value
    final String keyValue = conf.getString(key);

    // Does the property has a value
    if (keyValue == null || keyValue.isEmpty()) {
        return null;
    }

    // Map the property to the ENUM
    return Enum.*valueOf*(myClass, keyValue.toUpperCase());
}
```

最后，对于最后的测试，让我们通过加载 enum 来增强我们的`main`方法，并测试它来显示奶酪是否是我们最喜欢的:

```
public static void main(String[] args) {
    // Load and init the configuration library
   final Config conf = ConfigFactory.*load*();

   // Get the value of the JSON property
   final Cheese cheese =
                *getEnumProperty*(conf, "swiss.cheese", Cheese.class);

   if (Cheese.*GRUYERE*.equals(cheese)) {
       System.*out*.println(String.*format*("I really like %s 🧀", cheese));
   } else {
       System.*out*.println(String.*format*("%s is ok", cheese));
   }
}
```

瞧，就是这样，不多不少。

您可以尝试使用前面的命令行运行我们的项目，如果一切按计划进行，输出应该如下所示:

```
[INFO] -------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] -------------------------------------------------------------
[INFO] Total time:  2.437 s
[INFO] Finished at: 2019-08-16T15:43:42+02:00
[INFO] -------------------------------------------------------------
I really like GRUYERE 🧀
```

# 蛋糕上的樱桃🍒🎂

如果你想避免创建自己的项目和复制上述代码的麻烦，我已经在网上发布了该项目，请便。

```
$ git clone https://github.com/peterpeterparker/json-to-enum.git
```

到无限和更远🚀

大卫