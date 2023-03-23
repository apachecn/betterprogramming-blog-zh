# 不同类型的浏览器存储

> 原文：<https://betterprogramming.pub/the-different-types-of-browser-storage-82b918cb3cf8>

## 了解可用于在用户浏览器上保存用户数据的不同工具

![](img/3730b4802b1bd868f3f9fa2ce33a5731.png)

[@ kundeleknabiegunie](http://twitter.com/kundeleknabiegunie)unsplash.com

现代网络浏览器为在用户的浏览器上存储网站数据提供了不同的选项，允许根据需要检索这些数据。这使网站所有者能够长期保存数据，保存网站内容或文档以供离线使用，存储用户首选项，应用状态等。

在本教程中，让我们讨论不同类型的浏览器存储，可用于在用户的浏览器上存储网站数据。

## 浏览器存储的使用案例

*   个性化站点首选项
*   持续的站点活动
*   存储登录状态
*   将数据和资产保存在本地，这样网站可以更快地下载或在没有网络连接的情况下使用
*   在本地保存 web 应用程序生成的文档以供脱机使用
*   提高网站性能
*   减少对后端服务器的请求

## 浏览器存储的类型

*   饼干
*   局部存储器
*   会话存储
*   索引 b
*   Web SQL
*   高速缓冲存储

# 饼干

这是在客户机上存储数据的传统方法——这是 HTML5 之前的 web 存储的唯一选择。

Cookies 存储客户端数据，为网站访问者提供个性化体验。Cookies 是在服务器上创建的，并在响应时发送到客户端，并且在每次请求时都与服务器交换数据。服务器可以使用 cookie 数据向用户发送个性化内容。

还可以通过 JavaScript: `document.cookie`创建、更新或读取 cookies。`HTTPOnly` cookie 标志可用于限制 JavaScript 中的 cookie 访问，以缓解一些安全问题，例如跨站点脚本(cookie 只供服务器访问)。

## **Cookie 属性**

```
Set-Cookie: <cookie-name>=<cookie-value>
Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>
Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit>
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>
Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>
Set-Cookie: <cookie-name>=<cookie-value>; Secure
Set-Cookie: <cookie-name>=<cookie-value>; HttpOnlySet-Cookie: <cookie-name>=<cookie-value>; SameSite=Strict
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Lax
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=None; Secure// Multiple attributes are also possible, for example:
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>; Secure; HttpOnly
```

有两种类型的 cookie:会话 cookie 和持久 cookie。

**会话 cookie**

会话 cookies 不指定`Expires`或`Max-Age`属性，并且在浏览器关闭时被删除。

**持久 cookie**

持久 cookies 指定了`Expires`或`Max-Age`属性。关闭浏览器时，cookies 不会过期，但会在特定的日期(`Expires`)或时间长度(`Max-Age`)过期。

根据 cookie 头中的域设置，一个域上设置的 cookie 可以被其他子域访问。

```
Set-Cookie: test=test-value; Domain=example.com - the cookie is available for example.com and sub domains
```

## **对 cookies 的限制**

*   只能存储 4 KB 的数据—此限制会因浏览器的实施而有所不同
*   一个域中允许的 cookies 数量受浏览器实现的限制(例如，20 个)
*   跨域的 cookies 总数受到限制(例如，300)。一旦达到限制，最旧的 cookie 就会被删除，以便存储新的 cookie。该数量基于浏览器实现。
*   每次请求时，Cookie 数据都会发送到服务器。这将消耗额外的带宽并影响性能。
*   允许与第三方共享数据(例如，第三方 cookies)

Cookies 会导致多种安全问题，因此现在建议尽可能使用现代存储 API。

# Web 存储 API

Web 存储 API 允许 web 应用程序在用户浏览器中本地存储数据。API 是作为 HTML5 标准的一部分启用的。

与 cookies 相比，存储限制更大，例如至少 5 MB(实际大小取决于浏览器的实现)。这些信息仅在客户端提供，不会与服务器共享。服务器没有任何权限修改数据。E

数据不能在域之间共享，包括子域。每个源(协议和域的组合)都有唯一的存储—所有的 API 操作都在特定于源的存储中执行。

Web 存储 API 提供了两种不同的对象来在用户的浏览器上存储数据:`sessionStorage`和`localStorage`。

## 本地存储

`localStorage`对象存储没有截止日期的数据。当浏览器关闭时，数据不会被删除，并将在第二天、第一周或第二年可用，直到被网站或用户删除。

## 会话存储

`sessionStorage`对象等同于`localStorage`对象，只是它只存储一个会话的数据。当用户关闭特定的浏览器选项卡时，数据被删除。

Web 存储 API 将数据存储为键/值对。所有数据都存储为字符串，而所有添加到存储中的数据都隐式转换为字符串。它在检索时显式地将数据转换为所需的类型。`JSON.parse()`和`JSON.stringify()`方法可用于存储中对象数据的序列化和反序列化。

Web 存储 API 调用是同步的，因此它们可能会影响 UI 呈现。使用 Web 存储 API 来存储和检索最少量的数据。Web 存储 API 很容易用于在用户的浏览器上存储和检索数据——所有现代浏览器都支持 Web 存储 API。

# 索引数据库存储

IndexedDB 是一个基于 JavaScript 的面向对象的数据库。IndexedDB 允许您存储和检索用键(主键，例如 SSN)索引的对象。可以存储结构化克隆算法支持的任何对象(例如，视频、图像)。IndexedDB 使用起来比 Web 存储 API 复杂得多。

IndexedDB 是一种在用户浏览器中持久存储大量数据的方法。IndexedDB 允许您创建具有高级功能的 web 应用程序，而不管网络是否可用。这些应用程序可以在线和离线工作。IndexedDB 对于存储大量数据的应用程序和不需要持续互联网连接的应用程序非常有用。

IndexDB API 是异步的，它不会阻止 UI 呈现。这个 API 使用索引来实现数据的高性能搜索。

创建数据库模式和对象，打开到数据库的连接，然后在一系列事务中检索和更新数据。IndexDB 允许存储大量的结构化数据。该大小基于浏览器实现。

该数据库是 origin 的私有数据库，因此任何其他网站都无法访问 IndexedDB 存储的其他网站。

# Web SQL 数据库

> " Web SQL 数据库是一个网页 API，用于存储数据库中的数据，可以使用 SQL 变量进行查询。"— [维基百科](https://en.wikipedia.org/wiki/Web_SQL_Database)

该规范基于 SQLite。并不是所有的浏览器都支持 Web SQL 数据库——这个标准现在被 W3C 否决了，IndexDB 应该是一个替代方案

尽管如此，这仍可以在 Safari、Chrome、Opera 和 Edge 等受支持的浏览器中使用。

# 缓存存储

> " CacheStorage 是浏览器中的一种存储机制，用于存储和检索网络请求和响应。它存储一对请求和响应对象，请求作为键，响应作为值。
> 
> — [奇杜梅·恩纳迪](https://medium.com/u/a1ee806e09de?source=post_page-----82b918cb3cf8--------------------------------)途经[零碎](https://blog.bitsrc.io/introduction-to-the-cache-storage-a-new-browser-cache-pwa-api-a5d7426a2456)

CacheStorage API 可以在 Windows 上下文(DOM 上下文)中使用，也可以与服务工作器 API 一起使用，以支持离线访问。在本教程中，我们将更多地讨论 DOM 上下文。

创建 CacheStorage 是为了使网站能够存储网络请求和响应，但它也可以用作存储工具。例如，我们可以将用户偏好等自定义数据存储到缓存中，并在需要时检索它们。`put`方法可用于将自定义响应对象存储到缓存存储中。

CacheStorage API 允许我们从跨源网站获取并缓存数据。CacheStorage API 是异步的，不会阻止 UI 呈现。CacheStorage 选项是浏览器存储的最新添加，一些浏览器仍然不支持它。

有关不同储物选项的更多详情，请参考以下视频。

关于存储演示，请参考[浏览器存储演示](https://github.com/techforum-repo/youttubedata/tree/master/browser-storage-demos)(该演示构建在带有 Express.js 的 Node.js 上。

在用户浏览器上存储数据有多种选项，请根据您的使用情况选择选项。

使用 CacheStorage API 存储数据以供脱机访问。

对于存储大量应用程序或用户生成的数据，IndexedDB 是更好的选择。

Cookies 仍然可以用来存储服务器识别状态所需的最少数据。

本地存储和会话存储可用于存储最少量的数据。本地存储和会话存储 API 是同步的，因此它们会影响 UI 呈现。但是在项目中启用 API 是很容易的。