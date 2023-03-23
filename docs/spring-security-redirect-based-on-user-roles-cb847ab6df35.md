# Spring Security —基于用户角色的重定向

> 原文：<https://betterprogramming.pub/spring-security-redirect-based-on-user-roles-cb847ab6df35>

## 基于 spring 用户角色将用户定向到页面

![](img/c96896770fb25c199471492fa4a42e39.png)

亚历山大·席默克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

到目前为止，我们已经构建了一个基本的 spring boot 应用程序，启用了 spring 安全性，并创建了一个基本的登录表单。在上一课中，我们对第一课进行了扩展，添加了不同的用户角色，并根据这些角色显示和隐藏前端内容[(用户角色和百里香附加)。](https://mwhyte.dev/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75)

今天，我们将了解在不同角色的用户登录后，如何将他们重定向到不同的页面。

# Admin.html

在前面例子的基础上，我们现在创建了一个名为`admin.html`的新 HTML 文件。当管理员用户登录时，我们会将他们重定向到这个新页面。

[源代码](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role)

# MvcConfig

现在，为了提供新的`admin.html`页面，我们必须将这个页面添加到我们的`MvcConfig`类中。

和前面的例子一样，这是通过创建一个类、扩展`WebMvcConfigurerAdapter`和覆盖`addViewControllers`方法来实现的。这次添加了我们应用程序的所有早期页面和新的管理页面:

[源代码](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role)

# WebSecurityConfig

**建造师**

决定当不同的用户角色登录时做什么。我们已经创建了一个类型为`AuthenticationSuccessHandler`的新字段。我们通过构造函数注入来设置这个新的配置 bean。

**配置方法**

这个方法负责显式地覆盖和配置`HttpSecurity`。从上一个例子中，我们添加了两行。

首先，我们在`authorizeRequests`部分下添加了一个新的 antMatcher，我们告诉 spring security 只允许拥有`"ADMIN”`角色的用户访问所有以`“/admin”`开始的端点:

```
.antMatchers("/admin").hasRole("ADMIN")
```

其次，我们在`formLogin`部分下添加了我们的`CustomAuthenticationSuccessHandler`,告诉 spring security 询问这个`CustomAuthenticationSuccessHandler`在成功登录时该做什么:

```
.successHandler(authenticationSuccessHandler)
```

## **配置全局方法**

`configureGlobal`方法是我们的用户内存注册表。我们添加了两个用户。一个扮演主要的`USER`角色，另一个扮演`ADMIN`角色。

完整示例:

[源代码](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role)

# customauthenticationsuccesshandle

正如您在下面的示例代码中看到的，这个类实现了 springs `AuthenticationSuccessHandler`类并覆盖了`onAuthenticationSuccess`方法。

一旦用户成功登录，就会调用`onAuthenticationSuccess`，并检查用户的角色。如果用户的角色是`admin`，我们重定向到`/admin` HTTP 端点；否则，我们将它们重定向到`/index`端点。

此时，我们的`MvcConfig`接管并基于我们之前创建的 viewController 提供正确的 HTML 页面。

[源代码](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role)

# 演示

检查[出源代码](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role)，打开应用类，右键运行演示。

要开始这个示例，您的机器上需要有端口 8080。如果不是，您可以使用以下命令在 application.properties 文件中更改此默认值:

```
server.port=8081
```

将它设置为您想要的任何值。

或者，您可以观看这个短片:

# 单元测试

与往常一样，我们修改并添加了一些额外的测试来涵盖新功能:

下面是我们正在使用的 Spring 测试支持的快速回顾:

*   `@ExtendWith(SpringExtension.class)` —告诉 JUnit5 在 Spring 的测试支持下运行单元测试
*   `@SpringBootTest` —作为 spring boot app 运行。即装载`application.properties`和弹簧豆
*   `@AutoConfigureMockMvc` —创建一个名为 MockMvc 的测试助手类。我们可以模拟一个前端客户端从这里向服务器发出请求。
*   `@WithMockUser` —提供模拟某些用户的能力，在我们的例子中是经过身份验证的用户。
*   `FormLoginRequestBuilder` —允许我们创建基于表单的登录请求的实用程序类。

接下来，我们将讨论 spring security 的跨站点请求伪造(CSRF)保护。

不是中等会员？支持我成为其中一员:

[](https://mwhyte.dev/membership) [## 通过我的推荐链接加入 Medium 迈克尔威特

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mwhyte.dev](https://mwhyte.dev/membership) 

# 资源

[](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role) [## GitHub-mwhyte-dev/spring-security at 3 . redirect-based-on-role

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mwhyte-dev/spring-security/tree/3.redirect-based-on-role) [](/spring-security-basic-login-form-7c8f6e6e9f56) [## 用 Spring Security、百里香和 Java 构建一个基本的登录表单

### 简化登录表单

better 编程. pub](/spring-security-basic-login-form-7c8f6e6e9f56) [](/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75) [## Spring Security —用户角色和百里香附加功能

### 基于 spring 用户角色显示和隐藏内容

better 编程. pub](/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75)  [## 春天安全

### 提高您的 Java 代码水平，探索 Spring 能为您做什么。

spring.io](https://spring.io/projects/spring-security) [](https://www.thymeleaf.org/doc/articles/springsecurity.html) [## 百里香+ Spring 安全集成基础

### 你是否已经切换到百里香，但你的登录和错误页面仍然使用 JSP？在本文中，我们将了解如何…

www.thymeleaf.org](https://www.thymeleaf.org/doc/articles/springsecurity.html)