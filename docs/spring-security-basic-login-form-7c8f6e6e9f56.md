# 用 Spring Security、百里香和 Java 构建一个基本的登录表单

> 原文：<https://betterprogramming.pub/spring-security-basic-login-form-7c8f6e6e9f56>

## 简化登录表单

![](img/10b235da2fc238ba3bfa8d600cb1e56b.png)

照片由 [Aniket Bhattacharya](https://unsplash.com/@aniket940518?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Java 开发者们好！如果您正在开发基于 Spring boot 的应用程序， [Spring Security](https://spring.io/projects/spring-security) 是保护基于 Spring 的应用程序的事实上的标准。

在这个简短的教程中，我们将学习使用 Spring Boot、Spring Security 和百里香构建一个基本的登录表单。

首先，让我们简要介绍一些不太感兴趣的项目文件:

*   `*Application.java*` 包含了我们的 main 方法和`@SpringBootApplication`注释。启动 spring boot 应用程序的标准方式。
*   一个 CSS 样式表位于`src/main/resources/static/css/`下，使演示更加漂亮
*   一些百里香 HTML 模板也可以在`src/main/resources/`下找到，用于演示目的
*   最后是位于`src/main/resources`下的`*application.properties*`文件，在这里我们可以将配置参数传递给我们的 spring boot 应用程序。配置选项的完整列表可在[这里](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)找到

# 属国

与所有的 Spring boot 应用程序一样，有许多“Starter”库可以轻松地将 jar 添加到您的类路径中:

*   `spring-boot-starter-parent` —引入所有必需的 spring 依赖项并管理它们的版本
*   `spring-boot-starter-thymeleaf` —添加百里香叶-spring5 和百里香叶-extras-Java 8 时间依赖关系([更多关于百里香叶](http://www.thymeleaf.org/))
*   `spring-boot-starter-security` —添加 spring-security-config、spring-security-web 和 spring-aop 依赖项
*   `spring-boot-starter-test` —增加弹簧靴测试、junit、 [hamcrest](http://hamcrest.org/) 和 [mockito](https://site.mockito.org/)
*   `spring-security-test` —增加了模拟用户和用户角色的功能

以下是完整的 pom.xml 文件:

[pom.xml](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login)

# MvcConfig

[MvcConfig.java](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login)

`MvcConfig.java`类实现了 Spring 的`*WebMvcConfigurer*` 接口。这允许你覆盖`*addViewControllers*`(以及其他)方法，这是一种配置简单自动控制器的方法。在这个例子中，我们将它们映射到我们的百里香视图中(在`src/main/resources`下)。

# WebSecurityConfig

[WebSecurityConfig.java](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login)

这个类中发生了很多事情，因为它包含了启用和配置我们的基本登录表单的主要安全配置。

扩展`WebSecurityConfigurerAdapter`
另一个抽象基类，允许你覆盖 spring security 默认配置的某些方面。在我们的例子中，我们将覆盖方法`configure`。

`configure(HttpSecurity http)`方法
注意传递给这个方法的参数。有几种不同的`overloaded`配置方法。我们将特别覆盖和配置`*HttpSecurity*`

*   `*authorizeRequests()*` —允许我们配置 web 服务器上要保护的资源。从我们的示例代码中可以看到，我们允许对 CSS 目录进行不安全的访问，并要求所有其他资源都是安全的，只能由经过身份验证的用户访问。
*   `formLogin()`告诉 spring security 我们希望使用一个登录表单。如果认证成功，我们提供我们想要重定向到的 URL，最后，我们允许访问登录和注销端点。
*   我们正在禁用默认情况下启用的跨站点请求伪造保护。我们将在本系列的后面讨论这一点。

`configureGlobal` 方法
该方法允许我们在整个应用程序中全局自动连接一个`*AuthenticationManager*`类。对于这个例子，我们使用一个用户和一个用户角色的基本内存方法。

# 百里香命名空间

在我们的`login.html`文件中需要注意一些事情:

spring-security 名称空间:

```
<html  xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity3">
```

`thymeleaf`动作指示表单向提供的 URL(/log in)发出 POST 请求:

```
<form th:action="@{/login}" method="post" class="form login">
```

`thymeleaf`如果条件可以检查 URL 参数，出错并注销响应，如果返回 true 则显示内容:

```
<div class="text--center" th:if="${param.error}"> Invalid username and password. </div> <div class="text--center" th:if="${param.logout}"> You have been logged out. </div>
```

百里香网站[上的完整列表](http://www.thymeleaf.org/doc/tutorials/2.1/thymeleafspring.html#handling-the-command-object)

# 单元测试

[ApplicationTests.java](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login)

`@ExtendWith(SpringExtension.class)`
告诉 JUnit5 在 Spring 的测试支持下运行单元测试

`@SpringBootTest`
作为春季开机 app 运行。即装入`application.properties`和弹簧豆

`@AutoConfigureMockMvc`
创建一个名为 MockMvc 的测试助手类。我们可以模拟一个前端客户端从这里向服务器发出请求。

`@WithMockUser`
提供了模仿某些用户的能力——在我们的例子中是一个经过认证的用户。

`FormLoginRequestBuilder`
一个工具类，允许我们创建基于表单的登录请求。

# 演示

要运行这个演示，你可以在 [GitHub](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login) 上找到这个例子的完整代码

打开`Application.java`类并右键单击 run。端口`8080`需要在您的机器上可用才能开始演示。如果不是，您可以使用以下命令在 application.properties 文件中更改此默认值:

```
server.port=8081
```

将它设置为您想要的任何值。

或者，您可以观看这个短片:

[演示](https://youtu.be/aQ6QTQdCfeQ)

仅此而已。接下来，我们将讨论 spring security 的用户角色，以及基于用户权限隐藏和显示网站内容的能力。

[](/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75) [## Spring Security —用户角色和百里香附加功能

### 基于 spring 用户角色显示和隐藏内容

better 编程. pub](/spring-security-user-roles-and-thymeleaf-extras-c10113d4ad75) 

不是中等会员？支持我成为其中一员:

[](https://mwhyte.dev/membership) [## 通过我的推荐链接加入 Medium 迈克尔威特

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mwhyte.dev](https://mwhyte.dev/membership) 

# 资源

[](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login) [## GitHub-mwhyte-dev/spring-security at 1 . basic-form-log in

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mwhyte-dev/spring-security/tree/1.basic-form-login)  [## 春天安全

### 提高您的 Java 代码水平，探索 Spring 能为您做什么。

spring.io](https://spring.io/projects/spring-security) [](https://www.thymeleaf.org/doc/articles/springsecurity.html) [## 百里香+ Spring 安全集成基础

### 你有没有换成百里香，但是你的登录和错误页面还在用 JSP？在本文中，我们将了解如何…

www.thymeleaf.org](https://www.thymeleaf.org/doc/articles/springsecurity.html)