# 如何在 Spring Boot 和科特林实现自定义的基于令牌的身份验证

> 原文：<https://betterprogramming.pub/how-to-implement-custom-token-based-authentication-in-spring-boot-and-kotlin-5b59b55c1de2>

## 构建更安全的 Spring Boot 应用程序

![](img/3015e34b2d0455c5c12fdc7b9ef82cfe.png)

由[肖恩·林](https://unsplash.com/@seanlimm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

当构建 [Spring Boot](https://spring.io/projects/spring-boot) REST web 服务时，我们必须[处理安全性](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc)。

[](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc) [## 为什么 DevSecOps 是 2021 年最大的技术趋势之一

### 安全性是 IT 企业成功的关键

medium.com](https://medium.com/technology-hits/why-devsecops-is-one-the-biggest-tech-trends-in-2021-b8d8c56e38fc) 

为了实现我们的安全目标，比如授权和认证，使用像 [Spring Security](https://spring.io/projects/spring-security) 这样专门设计的框架可能是最好的解决方案。

> “Spring Security 是一个专注于为 Java 应用程序提供认证和授权的框架。像所有 Spring 项目一样，Spring Security 的真正强大之处在于它可以很容易地扩展以满足定制需求。”–[弹簧安全](https://spring.io/projects/spring-security)

然而，有时实现一个特定的认证逻辑来保持应用程序的简单是必要的。

我们将要展示的系统将允许我们选择是否保护一个 API。此外，我们假设每个有效的认证令牌标识一个特定的用户。令牌位于特定的头或 cookie 中，由认证逻辑用来提取用户，该用户的数据将被自动传递给受保护的 API 的函数体。

让我们看看如何在 Spring Boot 和科特林实现定制的基于令牌的认证。

# 1.定义自定义注释

为了选择 API 是否应该受到身份验证系统的保护，我们将使用一个自定义的注释。该注释将用于标记类型为`User`的参数，以定义 API 是否受保护。由令牌标识的特定用户的实例被自动检索，并且可以在 API 函数体内使用。

让我们看看如何定义一个定制的`Auth`注释:

# 2.定义验证逻辑

认证逻辑应该放在一个特定的组件中，我们准备称之为`AuthTokenHandler` *。*该类的目的是验证令牌是否有效，并提取其相关用户。这可以通过多种方式实现。我们将展示两种不同的验证方法。

使用自定义 DAO:

调用外部 API:

在这两种情况下，当令牌丢失或无效时，会抛出一个自定义的`AuthenticationException`。在这种情况下，受保护的 API 应该响应“401 未授权”

> “HTTP 401 未经授权的客户端错误状态响应代码表示请求尚未应用，因为它缺少目标资源的有效身份验证凭据。”— [MDN 网络文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401#)

为了实现这一点，标记有`[@ControllerAdvice](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ControllerAdvice.html)`的类可以如下使用:

# 3.正在检索令牌

当定制的`Auth`注释被识别时，为了允许 Spring Boot 自动在头或 cookies 中寻找令牌，必须定义一个实现`[HandlerMethodArgumentResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/method/support/HandlerMethodArgumentResolver.html)`的`AuthTokenWebResolver`。

让我们假设身份验证令牌可以放在名为`authToken`的头或 cookie 中。检索逻辑可以如下实现:

# 4.配置 Spring Boot

现在，我们必须为配置定义一个定制类。这样，Spring Boot 将能够按照设计使用定制的`Auth`注释。

为了一切正常，我们需要将之前定义的`AuthTokenWebResolver`添加到默认的参数解析器中。这可以通过利用`[WebMvcConfigurationSupport](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurationSupport.html)`类来实现。

> “[WebMvcConfigurationSupport]通常通过将`@EnableWebMvc`添加到应用程序`@Configuration`类来导入。另一个更高级的选择是直接从这个类扩展，并根据需要覆盖方法，记住将`@Configuration`添加到子类中，将`@Bean`添加到被覆盖的`@Bean`方法中— [Spring 的官方文档](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurationSupport.html)

我们将定义一个扩展了`WebMvcConfigurationSupport`的`@Configuration`类:

当使用`WebMvcConfigurationSupport`，时，不要忘记我们必须处理 CORS 配置。否则，我们的 API 可能无法像预期的那样到达。

# 5.把所有的放在一起

现在，是时候看看如何使用`Auth`注释来使 API 只与经过身份验证的用户一起工作了。这可以通过向所选的控制器 API 函数添加一个标有`Auth`注释的`User`类型参数来轻松实现:

受基于自定义令牌的身份验证保护的 API

此外，定义缺乏保护的 API 也是可能的:

不受基于自定义令牌的身份验证保护的 API

# 奖金

使用非常相似的方法，您可以实现基本的访问认证，如这里的[所述](https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19)。

[](https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) [## 如何在 Spring Boot 实现基本接入认证

### 让您的 Spring Boot API 更加安全

levelup.gitconnected.com](https://levelup.gitconnected.com/how-to-implement-basic-access-authentication-in-spring-boot-eaded2e33d19) 

# 结论

就这些了，伙计们！我希望这有助于您在 Spring Boot 和科特林定义自定义的基于令牌的身份验证。

如果你有任何意见或建议，请告诉我。