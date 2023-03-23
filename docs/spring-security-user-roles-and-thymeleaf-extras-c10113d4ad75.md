# Spring Security —用户角色和百里香附加功能

> 原文：<https://betterprogramming.pub/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75>

## 基于 spring 用户角色显示和隐藏内容

![](img/db3797ade0d6cff618e17f798c0a74f3.png)

照片由 [Aniket Bhattacharya](https://unsplash.com/@aniket940518?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在上一课中，我们学习了如何使用 spring security 构建一个基本的登录表单。今天，我们将看看添加用户角色和百里香图书馆的一些不错的功能，以显示和隐藏基于这些角色的内容。

# 属国

与所有 Spring boot 应用程序一样，一些 starter 库使得向类路径添加 jar 变得很容易。

[pom.xml](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras)

# WebSecurityConfig

首先，我们将向应用程序的`WebSecurityConfig.configureGlobal`方法添加另一个用户。这次给他们一个新的角色`admin`。现有用户被授予用户角色`.roles("USER")`:

[WebSecurityConfig.java](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras)

现在，我们有两个可用的用户可以登录。每个用户都配置了不同的角色，我们现在可以使用这些角色来隐藏和显示不同的内容。

本课程的其余部分保持不变，关键点可以总结为:

*   `*authorizeRequests()*` —允许我们配置 web 服务器上的哪些资源需要保护。你可以从我们的示例代码中看到，我们允许对 CSS、图片目录和 favicon 进行不安全的访问。所有其他资源都是安全的，只有经过身份验证的用户才能访问。
*   `formLogin()`告诉 spring security 我们希望使用一个登录表单。
    如果认证成功，我们提供我们想要重定向到的 URL，最后，我们允许访问登录和注销端点。
*   我们正在禁用默认情况下启用的跨站点请求伪造保护。我们将在本系列的后面讨论这一点。

# 百里香叶:授权

接下来，在我们的 index.html 页面中呈现 div 之前，我们将使用百里香属性`sec:authorize`来检查用户角色。

为此，我们首先需要将百里香 XML 名称空间添加到我们的[index.html](https://github.com/mwhyte-dev/spring-security/blob/2.thymeleaf-extras/src/main/resources/templates/index.html)中。

```
<html  xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
```

然后我们可以使用`sec:authorize`属性来检查用户的角色(他们有权查看的内容):

```
<div sec:authorize="hasRole('ADMIN')">
    This content is only shown to administrators.§§
</div>

<div sec:authorize="hasRole('USER')">
    This content is only shown to users.
</div>
```

如您所见，属性`sec:authorize`被添加到每个 div 中，我们使用 spring 安全方言来检查用户的 spring 安全角色。只有登录用户拥有该角色时，才会呈现内容。即 *hasRole* 返回真。

值得注意的是，内容不仅是隐藏的，而且当我们的应用服务器将页面返回给浏览器时，内容也不会被呈现。

# 百里香叶:认证

百里香的另一个有用的特性是`sec:authentication`属性。该属性可以返回各种与安全相关的元数据。在下面的例子中，我们可以检索用户的用户名和角色，并在 HTML 中显示它们。

```
<div>
    User: <span sec:authentication="name">NOT FOUND</span>
    Spring Roles: <span sec:authentication="principal.authorities">NOT FOUND</span>
</div>
```

更多有用的属性见[百里香文档](https://github.com/thymeleaf/thymeleaf-extras-springsecurity)

# 演示

要运行演示，[检查源代码](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras)并打开应用程序类，右键单击 run。

要开始这个示例，您的机器上需要有端口 8080。如果不是，您可以使用以下命令在 application.properties 文件中更改此默认值:

```
server.port=8081
```

将它设置为您想要的任何值。

或者，您可以观看这个短片:

# 单元测试

我们增加了一些单元测试来涵盖这个新功能:

[ApplicationTests.java](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras)

我们已经创建了一个小的实用方法`doesNotContainString`。使用这个和 Hamcrests】方法，我们可以检查内容是否基于特定的用户角色被呈现。

至关重要的是，spring test 注释`@WithMockUserp`提供了模仿某些用户的能力——在我们的场景中，是一个具有`user`或`admin`角色的认证用户。

下面是我们正在使用的 Spring 测试支持的快速回顾:

*   `@ExtendWith(SpringExtension.class)` —告诉 JUnit5 在 Spring 的测试支持下运行单元测试
*   `@SpringBootTest` —作为 spring boot app 运行。即装载`application.properties`和弹簧豆
*   `@AutoConfigureMockMvc` —创建一个名为 MockMvc 的测试助手类。我们可以模拟一个前端客户端从这里向服务器发出请求。
*   `@WithMockUser` —提供模拟某些用户的能力，在我们的例子中是经过身份验证的用户。
*   `FormLoginRequestBuilder` —允许我们创建基于表单的登录请求的实用程序类。

[接下来](https://mwhyte.dev/spring-security-redirect-based-on-user-roles-cb847ab6df35)，我们将继续介绍 spring security 的用户角色，但这一次我们将把管理员重定向到他们自己的管理页面(`admin.html`)，并保护该页面，以便只有管理员用户可以访问它。

[](/spring-security-redirect-based-on-user-roles-cb847ab6df35) [## Spring Security —基于用户角色的重定向

### 基于 spring 用户角色将用户定向到页面

better 编程. pub](/spring-security-redirect-based-on-user-roles-cb847ab6df35) 

不是中等会员？支持我成为其中一员:

[](https://mwhyte.dev/membership) [## 通过我的推荐链接加入 Medium 迈克尔威特

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mwhyte.dev](https://mwhyte.dev/membership) 

# 资源

[](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras) [## GitHub-mwhyte-dev/spring-2 . thyme leaf-extras 的安全性

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mwhyte-dev/spring-security/tree/2.thymeleaf-extras)  [## 春天安全

### 提高您的 Java 代码水平，探索 Spring 能为您做什么。

spring.io](https://spring.io/projects/spring-security) [](https://www.thymeleaf.org/doc/articles/springsecurity.html) [## 百里香+ Spring 安全集成基础

### 你有没有换成百里香，但是你的登录和错误页面还在用 JSP？在本文中，我们将了解如何…

www.thymeleaf.org](https://www.thymeleaf.org/doc/articles/springsecurity.html)