# 用渐进的增强制作漂亮的有弹性的 JavaScript 应用

> 原文：<https://betterprogramming.pub/make-beautifully-resilient-javascript-apps-with-progressive-enhancement-d537c50ee4ed>

## 忽视渐进式改进可能会伤害你的转换。本文将向您展示如何在您的应用程序中构建弹性。

![](img/c5e101b23aeda1d3ae070e438da82e02.png)

图片来源:作者

我已经详细谈过了[渐进增强](https://en.wikipedia.org/wiki/Progressive_enhancement)的好处和优点。我今天不会花太多时间来解释它，但它可以归结为:至少为尽可能多的用户提供工作体验，并为那些浏览器和设备支持这些增强功能的用户(jazz hands)增添趣味。

对我来说，点头附和很容易，但实际上我们总是失败。这并不奇怪。毕竟，作为创作者，利用最新技术创造新颖、引人入胜的体验是很诱人的。没有以前的经验，很难知道我们需要注意哪些怪癖。

# 有什么问题？

为了描绘一幅更真实的画面，让我们来看一个我处理过的现实生活中的例子。

不久前，我在一个看起来非常有趣的网站上，我想，“是啊，我为什么不注册呢？”。我点击了“注册”按钮，你知道发生了什么吗？没什么。

很自然地，我又点击了五次，然后打开我的开发工具，在 JavaScript 控制台中看到了一大块红色文本。

该网站使用[哨兵](https://sentry.io/)的错误跟踪脚本来捕捉任何 JavaScript 错误(聪明的想法)。问题是，我使用的浏览器扩展阻止了第三方追踪器。网站上的 JavaScript 依赖于 Sentry 的代码。当它被封锁时，一切都爆炸了，我无法注册服务(大概是最重要的事情)。

虽然解决方案可能是更加小心地管理 JavaScript 依赖关系，但这个故事强调了一个错过的实践渐进式增强的机会。

这是我看到应用程序失败的最普遍的情况；依靠 [JavaScript](https://austingil.com/category/javascript) 在浏览器和服务器之间来回发送数据(通常称为 JSON)。

例如，页面上的一个`<button>`，当它被点击时，用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 触发 HTTP 请求。它可能看起来像这样:

```
document.querySelector('button').addEventListener('click', () => {
  fetch('https://someapi.com', {
    method: 'POST',
    body: someBodyData
  })
})
```

这带来了优雅而有效的用户体验。他们点击一个按钮，数据就飞向母船。

但是完全依赖 JavaScript 发送这些数据有一个问题。用户浏览器中的 JavaScript 可能不适用于您的应用程序。

每当我提到这一点，回答总是:

> *谁把 JavaScript 关了！？*

这完全没有抓住重点。是的，有些用户实际上可能会禁用 JavaScript，但我并不太担心他们。他们知道他们报名参加的是什么。

尽管如此，JavaScript 可能会给其他用户带来问题(见[每个人都有 JavaScript，对吗？](https://kryogenix.org/code/browser/everyonehasjs.html))。以下是它可能失败的几种方式:

*   用户可能禁用了 JavaScript。
*   浏览器可能无法识别 JavaScript 语法(可能是旧的(浏览器，而不是用户))。
*   浏览器扩展阻止脚本运行(
*   用户可能有一个超时的慢速连接(移动数据)。
*   用户可能有间歇性连接(在火车上)。
*   该设备可能位于防火墙后面。

这并不是 JavaScript 可能失败的全部原因，但是如果其中任何一种情况发生，您就可以和这种美妙的用户体验说再见了。用户可能会看到一个按钮，但它不会做任何事情。

换句话说，如果您的应用程序只能使用 JavaScript，那么在很多情况下它都不能工作。你不仅是在伤害你的用户，还可能对你的目标产生负面影响。

那么如果我们不使用 JavaScript 呢？

几十年来，HTML 已经能够使用和发送 HTTP 请求，但是我将只关注`<form>`。这里有一个非常简单的例子:

```
<form>
  <label for="input-id">Label<label>
  <input id="input-id" name="key" />
  <button type="submit">Submit</button>
</form>
```

如果您在浏览器中打开这个 HTML，您会看到一个熟悉的 input 元素，标签为“label ”,后面跟着一个“Submit”按钮。单击该按钮会将浏览器重新加载到表单所在的 URL，但是会将输入中的名称和值作为查询字符串附加到 URL(从技术上讲，这是导航，而不是重新加载)。

我们还可以通过提供属性将数据发送到不同的 URL，并通过将属性设置为“POST”在请求正文中发送数据。

很靠谱，但是用户体验 meh。浏览器导航到目标 URL，导致整个页面刷新。有效果，但是不太性感。

我们都已经习惯了在浏览器不刷新的情况下进行交互。所以要求人们回到只使用`<form>`进行 HTTP 请求是不可能的。

# 有什么解决办法？

好消息是我们不必在 HTML 和 JavaScript 之间做出选择。我们两个都可以用！

让我们构建一个披萨订购表单。提交后，我们希望将请求正文中的数据发送到 URL“https://API . pizza . com”。在请求正文中，我们将包括姓名、电子邮件地址和首选浇头。

# 从 HTML 开始

这将是最直接的部分。毕竟，几十年来事情就是这样运作的，所以我们不需要做任何手势来让它运作。它只是工作。这就是 HTML 的妙处。

我们告诉表单将数据发送到哪里，并使用 POST 方法。然后在表单内部，每个输入都有自己的和一个属性。

标签对于可访问性是必需的，并且通过属性和输入的属性与它们各自的输入相关联。

由于功能原因，`name`属性是必需的。它告诉表单如何引用该数据。一些输入也共享同一个`name`，这一点很重要，因为它允许同一个数据属性有多个值。

除了做我们想做的事情(发送数据到一个 URL)之外，使用 HTML 表单也给了我们一些内置在浏览器中的优势。依靠辅助技术，浏览器可以向用户传递重要的语义/可访问性信息，我们甚至可以免费获得[客户端表单验证](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation)。

它不是最好的验证工具，但是用户下载它不需要任何费用。我们还可以逐步增强验证体验，但这超出了本文的范围。

# 用 JavaScript 匹配特性奇偶校验(有点棘手)

接下来，我们将使用 JavaScript 为[“提交”事件](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit_event)附加一个事件监听器。在事件处理程序中，我们可以阻止正常的 HTML submit 事件运行，并用 JavaScript 替换相同的功能。

棘手的部分是确保用 JavaScript 发出的**请求和用 HTML** 发出的请求一样。所以浏览器决定了我们需要用 JavaScript 重新构建什么，以保持特性的对等性。换句话说，我们不想以使其他事情变得更糟为代价来使一些事情变得更好。

让我们一步一步地分解它。为了给表单附加一个事件处理程序，我们需要一个表单 [DOM 节点](https://developer.mozilla.org/en-US/docs/Web/API/Node)。为此我们可以使用`[document.querySelector](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)`。一旦我们有了 DOM 节点，我们就可以用`[node.addEventListener()](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)`附加事件处理程序。

```
document.querySelector('form').addEventListener('submit', (event) => {
  // Event handler goes in here
}
```

我们想使用`fetch` API 发出 HTTP 请求。为此，我们需要知道 URL、数据和可选的请求方法。对于 GET 请求，我们可以发送 URL 中的所有数据。对于 POST 请求，我们需要传递一个具有`method`和`body`属性的对象。

方便的是，如果 HTML 处理得当，我们可以得到我们需要的所有信息。

```
const form = event.target;
const url = new URL(form.action || window.location.href);
const formData = new FormData(form);
const options = {
  method: form.method,
};
```

*   `<form>` DOM 节点可作为事件的。
*   URL 来自于`form.action`属性。如果没有定义，默认的 HTML 行为是使用当前的 URL。所以我们可以默认为`[window.location](https://developer.mozilla.org/en-US/docs/Web/API/Window/location).href`。稍后，我们将使用一个 URL 对象来进行简单的修改。
*   API 使得从任何形式捕获数据变得容易(只要输入具有`name`属性)。
*   请求方法可从`form.method`属性获得。默认为`'get'`。我们将它存储在一个对象中，以便于传递给`fetch`调用。

接下来，我们需要确定如何实际发送数据。如果请求应该使用 POST 方法，我们希望在`fetch`的 options 对象中为请求添加一个“body”。否则，我们希望将 URL 中的数据作为查询字符串发送。

这比听起来要复杂，因为在 POST 请求中，我们不能只分配一个`FormData`对象作为请求体。这样做实际上会将请求的`Content-Type`头修改为`'multipart/form-data'`，这可能会破坏您的 HTTP 请求(稍后会详细介绍)。

幸运的是，网络平台还有另一个方便的工具`[URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)`(老实说，这可能是这场秀的明星)。我们可以使用一个`URLSearchParams`对象作为请求体，而不需要修改头部。我们还可以用它来构造 GET 请求的查询字符串。得心应手！

好的，更多代码…

```
if (options.method === 'post') {
  options.body = form.enctype === 'multipart/form-data' ? formData : new URLSearchParams(formData);
} else {
  url.search = new URLSearchParams(formData);
}
```

*   对于 POST 请求，我们将在请求体中发送数据。
*   如果表单显式地将设置为`'multipart/form-data'`，那么在主体中使用`FormData`是安全的。
*   否则，我们可以退回到`URLSearchParams`。
*   对于 GET 请求，我们将在带有`URLSearchParams`对象的请求 URL 中发送数据。

同样，我们需要特别小心不要修改默认的浏览器行为，尤其是在请求体周围。HTML 表单可以通过分配属性来修改`[Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)`请求头。默认的是`'application/x-www-form-urlencoded'`，但是如果你需要在一个表单中发送文件，你必须使用`'multipart/form-data'`。

这很重要，因为许多后端框架默认不支持`'multipart/form-data'`。因此，除非您正在发送文件，否则最好坚持使用默认设置。

进入最后冲刺阶段。

我们已经有了所有需要的数据和配置。最后一部分是执行`fetch`的请求。

```
fetch(url, options)
event.preventDefault();
```

*   有了上面定义的 URL 和选项，我们可以将它们传递给`fetch` API。
*   执行`[event.preventDefault](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)`方法来防止 HTML `<form>`也提交和重新加载页面。

你可能看过其他教程，想知道为什么我们要等到最后一刻才调用`preventDefault`方法。即使这样也是一个谨慎的考虑。

考虑在我们的事件处理程序中可能隐藏了一个 JavaScript 错误。如果我们在第一行调用了`preventDefault`，并且错误发生在我们的`fetch`调用之前，那么脚本将会中断，HTTP 请求将永远不会被发送。通过等到前面所有的 JavaScript 都执行完，我们可以确保在阻止“提交”事件之前没有错误。或者，在出现错误的情况下，浏览器仍然会退回到以传统方式提交表单的默认行为。

完整的脚本可能如下所示:

考虑到花了多少心思和精力，这有点乏味。但我想这是一件好事，因为这意味着只需一点 JavaScript，我们就可以增加更好的用户体验。因为 HTML 以声明的方式提供了 JavaScript 需要的所有信息，所以这种改进无需任何修改就可以应用于所有表单。

我们在启用 JavaScript 时给用户更好的体验，在禁用 JavaScript 或出现问题时给用户最小的工作体验。

**渐进增强 FTW！！！**

(我们也可以在客户端验证上做同样的事情，但是要做到正确有点复杂)

但是我们还没有完成。到目前为止，我们只讨论了数据**发送**的部分，但是数据**接收**的部分呢？

# 服务器上的功能改进

如果我们所做的只是向服务器发送数据，我们可以就此收工，但在大多数情况下，我们希望向用户显示一些更新。要么是页面上的一些数据发生了变化，要么是我们想通知用户他们的请求成功了。

在纯 HTML 的世界里，浏览器要么导航到一个新页面，要么导航到同一个 URL。无论哪种方式，导航事件都会在服务器上重新构建 HTML，并将其作为响应发送回用户。所以显示应用程序的更新状态非常容易。

使用 JavaScript，我们无法获得整个页面重新呈现的好处。从技术上来说，浏览器仍然可以用页面的 HTML 来响应，我们可以使用 JavaScript 来重新绘制页面，或者我们甚至可以手动触发页面重载，但这又有什么意义呢？

用 JSON 响应要常见得多(也更可取)。但这也带来了自身的困境。如果我们用 JSON 响应，默认的 HTML 表单提交将重新加载页面，并向用户显示一堆废话。

如果我们可以用 HTML 响应 HTML 请求，用 JSON 响应 JavaScript 请求，会怎么样？

**嗯，我们可以！**

当一个 HTTP 请求从客户机发送到服务器时，会有一些附加数据跟着一起发送，而开发人员或用户不需要做任何事情。这个数据的一部分是 [HTTP 头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)。

最酷的是，在大多数现代浏览器中，有一个名为`[Sec-Fetch-Mode](https://w3c.github.io/webappsec-fetch-metadata/#sec-fetch-mode-header)`的头，它告诉服务器`[Request.mode](https://developer.mozilla.org/en-US/docs/Web/API/Request/mode)`。有趣的是，对于用 JavaScript 发出的请求，该值被设置为`cors`，而对于用 HTML 发出的请求，该值为`navigate`。

坏消息是 IE 11 或 Safari 不支持它[。嘘！](https://caniuse.com/mdn-http_headers_sec-fetch-mode)

好消息是，我们仍然可以通过让 JavaScript 开发人员多做一点工作来检测响应类型。

当我们创建一个`fetch`请求时，第二个[参数](https://developer.mozilla.org/en-US/docs/Web/API/fetch#parameters)是一个配置对象。在配置内部，我们可以定制请求头。不幸的是，我们不能在这里自定义`Sec-Fetch-Mode`头(浏览器不允许这样做)，但是我们**可以**设置`[Accept](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)` [头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)。

这个方便的小标题让我们明确地告诉服务器我们想要什么样的响应。默认值是`*/*`(喜欢，随便哥们)，但是我们可以设置成`application/json` (JSON 拜托！).我们需要手动将它添加到每个请求中，这有点烦人，但我认为这是值得的。

下面是我们新的`fetch`请求可能的样子:

```
fetch(url, {
  method: requestMethod,
  body: bodyData,
  headers: new Headers({ 'application/json' })
})
```

第一个参数仍然只是 URL。对于 POST 请求，第二个(init)参数应该已经存在，所以我们只需要添加或修改`headers`属性。对于 GET 请求，第二个参数可能还没有定义，所以我们可能需要在`headers`属性中包含它。请注意，虽然我在这里使用了构造函数，但是您也可以使用常规对象。

如果您在应用程序中发出大量的 HTTP 请求，手动将这些请求添加到每个请求中可能会过时。因此，我建议使用一个包装器或 [curried 函数](https://en.wikipedia.org/wiki/Currying)来自动完成 fetch。

此时，请求正在发送服务器需要的所有数据。现在，服务器需要处理请求并决定如何响应。对于 JSON 响应，我将把它留给您。对于 HTML 响应，我们可以返回相同的页面，或者简单地用一个[重定向](https://developer.mozilla.org/en-US/docs/Web/HTTP/Redirections)来响应。

您可以根据用户请求和应用程序逻辑来确定将用户重定向到哪里。或者，如果您只想将用户重定向回他们来的地方，我们还有一个 HTTP 头可以使用:。`Referer`头包含发出请求的 URL，由浏览器自动设置，不能被 JavaScript 修改，并且可用于所有请求。各方面都很完美。

示例时间！

这里我使用的是 [fastify](https://www.fastify.io/) ，但是这些概念应该适用于任何语言或框架:

*   创建接受 GET 或 POST 请求的服务器路由
*   我跳过了辛辣的商业逻辑，但那应该在你的回应之前(显然)
*   抓住`Accept`、`Sec-Fetch-Mode`和`Referer`标题
*   确定响应是否应该是 JSON。如果是，用 JSON 响应。注意，早期的`return`会阻止其余的执行。
*   否则，要么用 HTML 响应，重定向到新的 URL，要么重定向回请求的来源。在这种情况下，我做了后者。
*   注意，请求处理器必须接受`urlencoded`数据(HTML 默认编码)。它可以有选择地接受 JSON，但是如果你只接受 JSON，那么有效负载就很难用 JavaScript 创建，因此支持 HTML 就变得毫无意义。

在我的测试中，这真的很好，如果你愿意，你甚至可以创建一个插件(或[中间件](https://en.wikipedia.org/wiki/Middleware))来将这个逻辑添加到每条路由中。这样就不用每次都手动添加了。

所有这些的一个缺点(也许您已经发现了)是，如果目标是支持 HTML 表单，您只能支持 GET 和 POST 请求方法。

这令人失望，因为 PUT、PATCH 和 DELETE 是处理 [CRUD 操作](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)的非常方便的方法。好消息是，只要对 URL 模式稍加修改，我们就可以完成同样的事情，**几乎和**一样好。

下面是使用我们想要的任何方法的 API 的样子:

```
server.post('/api/kitten', create);
server.get('/api/kitten', readCollection);
server.get('/api/kitten/:id', readSingle);
server.patch('/api/kitten/:id', updateSingle);
server.delete('/api/kitten/:id', deleteSingle);
```

下面是只使用 GET 和 POST 方法的同一个 API:

```
server.post('/api/kitten', create);
server.get('/api/kitten/', readCollection);
server.get('/api/kitten/:id', readSingle);
server.post('/api/kitten/:id/update', updateSingle);
server.post('/api/kitten/:id/delete', deleteSingle);
```

*   GET 和 POST 路线不会改变。
*   修补和删除路由成为 POST。
*   我们将“方法”/更新和/删除附加到它们各自的路由中。

我承认我不喜欢这种权衡，但这是一个小烦恼，我想我会活下来。毕竟，这些好处(正如你希望看到的)是如此值得。

# 从这个例子中可以看出

这只是应用于 HTTP 请求的渐进增强的一个例子，但我认为这是一个重要的例子。以下是我认为你应该记住的一些事情:

*   表单只能发送 GET 和 POST 请求。如果不指定属性，它们默认发送 GET 请求到当前 URL。
*   默认情况下，数据作为 URL 编码的字符串(`text=hello&number=1`)发送到服务器，除非您更改`enctype`。对于 GET 请求，它放在请求 URL 中。对于 POST 请求，它放在请求正文中。
*   多个输入可以具有相同的名称，并且表单数据的同一数据属性可以有多个值。
*   当您提交表单时，大多数文本样式的输入将以空字符串的形式发送数据。也有一些例外。
*   `checkbox`和`radio`的默认值为“开”，即使未选中。如果未选中，则不包含数据。
*   `range`的默认值为‘50’。
*   `select`的默认值是第一次选择的`<option>`的值。如果选项没有值，将使用标记的内容。您可以通过省略禁用所有选项来避免发送默认数据。
*   默认情况下,`file`输入发送文件名。要将实际的文件作为二进制数据发送，请求的`Content-Type`必须是`**multipart/form-data**`，您可以用`enctype`属性来设置它。
*   `color`输入的默认值为`'#000000'`。
*   对于 JavaScript 提交来说，`FormData`和`URLSearchParams`是牛逼的。两者都可以在请求体中使用，但是使用`FormData`会将默认的`Content-Type`更改为`multipart/form-data`。
*   要在您的请求中包含额外的数据，您可以使用和输入“隐藏”的`name`、`value`和`type`。

将我们所学的应用到第一个例子中，一个点击时发送数据的按钮，我们可以更可靠地完成同样的事情，只需稍微修改一下 HTML:

```
<form action="https://someapi.com" method="POST">
  <input type="hidden" name="data-key" value="data-value">
  <button>Send data</button>
</form>
```

再加上我们的 JavaScript 表单提交者，我们将拥有一个现代的用户体验，如果页面遇到一些 JavaScript 问题，它仍然可以工作。

# 主要警告

没有一个解决方案是完美的，如果我说我上面推荐的没有缺陷，那是不诚实的。事实上，有一个非常大的。

依靠 HTML 表单构造数据意味着不能使用嵌套对象。

我的意思是，也许您可以创建名称中带有点或括号符号的输入(`<input name="user.first-name"> <input name="user.last-name">`)，然后在服务器上执行一些逻辑来构造一个嵌套对象。但是我自己没有做过，我也不确信这是值得的，所以我不会试图说服你。无论如何，如果您从一开始就考虑平面数据模型，这应该不是问题。

由于嵌套对象是不可能的，这也意味着您不能进行 GraphQL 查询。

在客户机和服务器上使用过 GraphQL 之后，不管有没有库，我都会说它超级酷，很有创新性，但我不喜欢它。对于大多数项目来说，它增加了更多的复杂性，并且成本超过了收益。

然而，很少有项目中有足够多的数据和 HTTP 请求围绕着 GraphQL 运行是有意义的。对于那些案子来说，是值得的。

(希望我没有让自己变成一个完全有偏见的傻瓜)

# 一万美元的问题

说明我的情况后，我将向你提出这个问题。如果您有一个只能使用 JavaScript 的结帐表单，那么是否值得重写，以便在 JavaScript 失败时返回到 HTML？嗯，看情况，对吧？

如果您的公司通过这个表单赚了 100 万美元，而这个表单由于 JavaScript 问题有 1%的几率失败，那么这个修复就值 1 万美元。

为什么是 1%？[根据 GOV.uk](https://gds.blog.gov.uk/2013/10/21/how-many-people-are-missing-out-on-javascript-enhancement/) 的研究，1%的用户无法使用 JavaScript。这是所有的用户(或页面浏览量)，而不仅仅是那些在旧手机上使用过时浏览器的人。有一个很好的解释者叫做[为什么可用性很重要](https://kryogenix.org/code/browser/why-availability/)，他解释了更多细节，但是现在我跑题了。

回到最初的问题，10，000 美元足够让你对那个表格做一些改变吗？对我来说是这样，这个决定是基于一个处理 100 万美元的表单。对一家公司来说，这实际上并不是太多的收入，大多数有表格的网站通常不止一个。

# 结束语

我在 HTML 和 JavaScript 中使用了相同的 HTTP 请求的例子来说明这个概念，但是需要指出的是，并不是所有的渐进式增强都是零和游戏。

它不总是工作或被打破。有时是关于工作出色或工作一般。有时它与安全性、性能、可访问性或设计有关。无论场景或技术如何，渐进增强都是关于在构建时考虑弹性回退。

新的浏览器提供了许多精彩的功能，但最重要的是，从适合大多数用户的东西开始，并为支持它的人提供增强的体验。以 CSS 为例，你可以咨询[caniuse.com](https://caniuse.com/)来检查浏览器兼容性，为旧浏览器编写样式，并在检测该特性的 at 规则中添加现代增强功能。

最后，我想用这句话来结束我的演讲。

> *这个代码是向前和向后兼容的。浏览器成为常青树，JavaScript 被迫向后兼容 web 的遗产是一个巨大的特性…许多开发团队浪费了很多时间来保持他们已经拥有的功能，因为他们在追逐依赖更新。这种技术是向前和向后兼容的，不会增加您的时间投资。编写一次，永远运行是 JS 的一个非常独特的特性，这是许多‘现代’框架所没有的。*
> 
> [T5【布莱恩勒鲁】T6](https://brian.io/)

我很喜欢这篇文章，并想看看我创作的更多类似的东西，下面是一些例子:

以下是我发现的一些特别有趣的资源:

[更新:我列出了 JavaScript 可能失败的原因，其中包括网络条件。接下来，我将介绍一个逐渐增强的表单提交示例。值得一提的是，JS 可能失败的原因列表是一个很好的常识，但是如果由于网络条件而出错，HTML 表单也有可能失败。]

非常感谢您的阅读。

```
**Want to Connect?**You can also [sign up for my newsletter](https://austingil.com/newsletter/) or [follow me on Twitter](https://twitter.com/heyAustinGil) if you want to know when new articles are published.*Originally published on* [*austingil.com*](https://austingil.com/resilient-applications-progressive-enhancement/)*.*
```