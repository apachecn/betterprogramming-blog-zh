# 每个 UI 脚本开发人员都应该知道的工具

> 原文：<https://betterprogramming.pub/tools-every-ui-script-developer-should-know-e13bb237d0c5>

## 这可以提高你每天的工作效率

![](img/178df980a788168ce7f1c0f9030adf75.png)

苏珊·霍尔特·辛普森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在 web 前端，大部分时间我们使用 JavaScript。因此，您可以使用其他人已经实现的一些工具，而不是自己编写工具并重新发明轮子(其中有一些错误)。所以，我想分享一些我经常使用的 npm 包(工具),我建议你去看看。

# Axios

Axios 包含一个从 web 资源请求数据的工具集，通常用于 API 调用。在我看来，它很容易在认证的场景中使用。它的[拦截器支持](https://axios-http.com/docs/interceptors)棒极了。例如:

如果您想在执行每个请求之前获取一个身份验证令牌。这里你可以使用拦截器。它将被全局分配，主要在根应用程序定义中(在 React 中)，定义可能如下所示:

```
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    const token = tokenHelper.GetToken();
    config.headers.common.Authorization = `Bearer ${token}`
    return config;
});
```

因此，从现在开始，每个请求都将执行这个拦截器，我将把令牌添加到授权头中。

# Moment.js

每次你处理日期或时间的时候，它都会，是的…以某种方式变得复杂。尤其是当您必须重新格式化日期并使用时间进行计算，或者检查当前日期值是在要检查的日期之前还是之后时。Moment.js 将为你提供一个非常方便的工具集，让你轻松完成这些任务。

让我们假设您用 moment.js“解析”了两个日期值

```
var date1= moment('01-02-2020 12:00:01.110')
var date2= moment('01-01-2020 12:00:01.111')
```

因此，您现在想要获取这些日期之间的小时数。当然，你现在可以循环通过日期，而最终日期将达到丑陋的方式。最好的方法是使用一行程序`date2.diff(date1,'hours')`。这将返回 23 小时的值，并且您不必创建复杂的算法来计算小时数(可能来自不同的时区等等)。

# 洛达什

有时处理数组或对象集合将是一件痛苦的事情。 [Lodash](https://www.npmjs.com/package/lodash) 是一款可以帮你解决这个问题的工具。所以我看到的主要优势是集合的处理。因此，让我们假设您有一个对象集合:

```
let data =[];
data.push({'name':'Jonny', amount:32.44});
data.push({'name':'Tom', amount:300.41});
data.push({'name':'Nadine', amount:400.22});
```

现在，您将只获得金额值超过 100 的项目。在这种情况下，可以使用这个`lodash`方法:

`_.filter(data,_=>_.amount=>100)`

在这种情况下，您将只获得 Tom 和 Nadine 的元素。这是使用 lodash 方法的一个简单例子，它还有许多其他的实现，比如聚合等等，更多信息请看他们的[文档](https://lodash.com/docs/4.17.15)。

# 数字. js

您在为不同地区设置数字格式时遇到过问题吗？例如，在德国，小数点是一个`,`，在英国或美国，小数点是一个`.`。在这种情况下，你可以用一个特定的小数点手动替换小数点，或者你可以编写自己的格式化工具，或者你可以使用[numeric . js](https://www.npmjs.com/package/numeral)。

好吧，格式化并不神奇，但是如果你从数据源得到一个空值呢？数字的力量来了。然后你可以用它定义一个默认的输出格式。这里有一个例子:

```
var nullNumber = numeral(null);

numeral.nullFormat('Is null');

var na = nullNumber.format('0.0')
// 'Is null'
```

这只是一个例子。Numeral.js 有非常好的[文档](http://numeraljs.com/)。看看这个。

```
**Want to Connect?** Say hello on [LinkedIn](https://www.linkedin.com/in/sascha-peter-bajonczak-32a17a2a/) or [GitHub](https://github.com/sbajonczak).
```