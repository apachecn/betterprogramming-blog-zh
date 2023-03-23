# 围棋中 YAML 的解析与生成

> 原文：<https://betterprogramming.pub/parsing-and-creating-yaml-in-go-crash-course-2ec10b7db850>

## 速成班

![](img/5e7f0a4254afebcb2ddd6a7cf5ea2349.png)

让我们去惹 YAML 吧！

本文以一个非常基础的程序为例，提供了一个在 Go 中解析和创建 YAML 的速成课程。

我不知道你怎么想，但是当我听到“封送”和“解封送”这样的术语时，我想到的最后一件事就是将 YAML 转换成一个结构。这就是我们生活的世界；命名很难，只要有机会，我们总会让事情听起来比实际情况更不寻常。

好消息是，需要一组(相对)小的工具来帮助您解析现有的 YAML 文件(解组)或将现有的 Go 结构(或任何类型)转换为 YAML(封送)。另一个好消息是，当向*解释让它变得简单*时，它很简单。

# **从 Go struct 到 YAML**

本文假设您的机器上已经安装并设置了 Go。如果你在这方面需要帮助，去[看看这个](https://go.dev/doc/install)指南，然后回来。

让我们创建一个基本项目:

```
mkdir converttoyaml
cd converttoyaml
go mod init converttoyaml/v1
```

接下来，让我们创建一个名为 *main.go* 的新文件，并添加以下内容:

一段简单的代码；创建一个结构，添加一些数据，并打印出来。一切都好，对吧？

现在，让我们假设这是一条我们希望以 YAML 格式保存的关键信息。为此，我们将使用 [go-yaml](https://github.com/go-yaml/yaml) 包。

首先，我们需要安装它:

```
go get gopkg.in/yaml.v3
```

接下来，我们需要更新我们的代码来导入包，然后让它工作。我们将从以下位置更新我们的导入行:

```
import “fmt”
```

To(如果有错误，我们将使用 log.fatal):

```
import (
  "fmt"
  "log"
  "gopkg.in/yaml.v3"
)
```

我们将更新我们的主要功能:

这里我们对 *go-yaml* 包的要求是获取我们的结构并将其转换成 yaml 输出。注意这里的 *out* 返回为*[]字节*；在 *fmt* 可以打印结果之前，我们需要将它转换成一个字符串。

因此，更新您的文件，重新运行该代码，您将看到如下输出:

```
topspeed: 60
name: Mirthmobile
cool: true
passengers:
- garth
- wayne
```

不错的 YAML！

# **从 YAML 到 Go struct**

既然微型移动电话的秘密已经在 YAML 被记录下来，我们需要将这些数据作为一个结构加载回 Go。首先，让我们将程序的输出写入一个文件:

```
go run ./main.go > themirth.yaml
```

为了将 YAML 文件的内容转换回我们的 *Car* struct，我们需要用一些针对 *go-yaml* 包的指令来注释我们的 struct 定义。为此，我们需要为每个结构字段指定输入键名:

这些标记使我们能够定义 YAML 中的键名和结构中相应的字段名的映射。当 YAML 文件被“解组”(即，被转换成结构)时，这些标签被用来适当地填充该结构。解组文件的代码如下所示(再次更新我们的主函数):

继续，复制并粘贴它！然后重新运行程序，您将得到如下输出:

```
{TopSpeed:60 Name:Mirthmobile Cool:true Passengers:[garth wayne]}
```

不错的结构！

# **处理 YAML Go 结构中的多种数据格式**

在理想的情况下，只有一种特定格式的输入数据，从 struct 字段到 YAML 键的静态映射也能很好地工作。但是因为这是真实的世界，这种事情不会总是发生。幸运的是，`go-yaml`包允许我们“自带”`UnmarshalYAML`功能。

我将使用的场景是`passengers`字段。有时加思和韦恩坐在一起，但当两人在为是否允许“吮吸切割”的创作者回到节目中而争吵时，他们可能不想花任何时间在一起。在这种情况下，Garth 不想提供一个名称数组；他想用一根绳子。

也就是这个

```
topspeed: 60
name: Mirthmobile
cool: true
**passengers: garth**
```

不是这个

```
topspeed: 60
name: Mirthmobile
cool: true
**passengers:
- garth**
```

继续用代码原样尝试；你会得到很好的回应:

```
unmarshal errors:
line 4: cannot unmarshal !!str `garth` into []string
```

是啊。有道理；数据类型现在已经完全改变了。然而，我们希望支持这一点，一个自定义的`UnmarshalYAML`函数是一个答案。

我们将在导入中添加一个新项目(错误):

```
import (
 "errors"
 "fmt"
 "log"
 "os" "gopkg.in/yaml.v3"
)
```

接下来，我们将添加自定义的`UnmarshalYAML`函数:

好吧，这是怎么回事。首先，当添加一个*unmarshal AML*方法到我们的结构中时，我们告诉`go-yaml`包跳过它的内部魔法，调用我们的函数来解析数据——所以我们必须比以前更加努力。

函数中第一个有趣的地方是*解组*函数本身。这个函数将我们的 YAML 转换成一个`map[string]interface{}`。然后，我们可以解包这个新值的内容(存储在 *carDetails* 变量中)。

对于我们要设置的每个字段，我们需要测试该字段是否实际存在，如果存在，我们就要获取值。现在，每个值都属于类型 *interface{}* ，所以我们必须将它转换为结构中所需的数据类型。

*一个快速的警告，你绝对需要在 Go 中做任何事情的时候做错误处理。任何转换为正确数据类型的失败都将导致死机。为了简洁起见，我在这里省略了它。*

一旦我们检查了我们的特殊乘客的领域，并验证我们有数据；下一步是使用一个开关来确定我们被提供了什么类型的数据。我们在这里的目标是获取一个字符串列表或单个字符串，并填充结构上的`Passengers`字段——这是一个字符串片段。

在示例代码中，如果提供的数据是任何类型的切片，我们迭代该数据，将其转换为字符串，并将其指定为乘客字段的成员。如果我们只被传递了一个字符串，我们将用我们的单个值给乘客字段分配一个新的字符串片段。在这样做的时候，不管 Garth 只传入单个字符串值还是一个字符串列表(你知道，当 Wayne 在的时候)，我们的代码都可以解析它并给出正确的值。

继续并重新运行该程序；事情看起来好多了，嗯？

# **处理 YAML Go struct 中的多种数据格式(更简单的方法)**

所以，在最后一个例子中，你对自己说，“这太多了”——这是你的权利。幸运的是，在另一个软件包的帮助下，我们可以更容易地做到这一点；[地图结构](https://github.com/mitchellh/mapstructure)。除了给我们带来像 Terraform 这样的美好事物之外，[米切尔](https://github.com/mitchellh)也给我们带来了`mapstructure`。mapstructure 包是一个更加灵活的解析器，可以处理我们以前处理的强制，而不需要太多额外的代码。

首先，我们来安装`mapstructure`:

```
go get github.com/mitchellh/mapstructure
```

现在，在 main.go 文件中，我们可以删除整个`UnmarshalYAML`函数；我们不再需要它了。我们确实需要稍微更新一下我们的汽车定义(将标签从`yaml`改为`mapstructure`):

最后，我们还有最后一个版本的`main`函数:

让我们回顾一下这里发生的事情:

*   我们不是直接将输入数据解组到`Car`，而是解组到`interface{} (variable raw)`。这意味着我们还没有尝试转换所有的数据类型，这样做的结果只是“幕后”变量`raw`是一个`map[string]interface{}`
*   我们正在创建一个新的`mapstructure`解码器，将它的配置设置为将结果发送到我们的 *c* 变量(类型为`Car`)，并且我们还通知`mapstructure`使用“`WeaklyTypedInput`”。这是关键；启用此选项将自动完成从单个字符串到字符串片段的转换，这是我们在自定义解组函数中必须完成的！
*   我们在新的解码器上调用`Decode`方法，它将我们的原始输入转换成具有正确数据类型的`Car`结构！

继续重新运行您的代码！

这个`mapstructure`包能够完成比这里所介绍的更多的工作，但是即使在这个简单的例子中，它也可以使处理输入 YAML(或者任何格式)变得更加简单！

# **结论**

我们已经讨论了很多内容，希望您对如何在 YAML 和围棋之间来回切换有一个基本的了解。值得回顾一下`go-yaml`和`mapstructure`文档；这两个包都有不同的方法或接口，可以使特定的用例变得更加容易，本文中的代码将是“困难的方式”

希望您发现这很有用！