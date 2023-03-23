# 用 JSON Web 令牌保护 Spring Boot REST API

> 原文：<https://betterprogramming.pub/secure-a-spring-boot-rest-api-with-json-web-token-reference-to-angular-integration-e57a25806c50>

## 加上角度积分的参考

![](img/b1096bd5b38db4fa47b88b7bd5e91ec7.png)

在本文中，我将带您了解如何使用 JSON Web Token (JWT)保护 Spring Boot REST API，以便在服务器和客户端之间交换声明。这是我和我的同事 Julia Passynkova 合作的第二部分，演示了如何使用 Spring Boot 作为 RESTful 后端来保护 Angular 2+应用程序。如果你不耐烦或者喜欢直接从代码中学习，查看一下 [Github 库](https://github.com/nydiarra/springboot-jwt)中的代码。你可以在这里找到角度积分项目[。](https://github.com/ipassynk/angular-springboot-jwt)

# **快速介绍**

在过去的几年里，Spring Boot 已经大大简化了 Spring Framework 应用程序的配置。它采用的这种随意的方法让开发人员只需在类路径上拥有 Spring 安全依赖就可以为应用程序启用基本的安全性。

选择 JWT 来保护您的 API 端点是一个很好的选择，因为它确保了客户端和服务器之间的无状态令牌交换，并且是紧凑的和 URL 安全的。有了 JWT，就没有必要在数据库中存储访问令牌(尽管您可能仍然会这样做，甚至需要这样做，这取决于用例)或者担心粘性会话。这使得在企业应用程序中构建冗余更具成本效益，至少在安全性方面是如此。但是，您确实需要处理其他方面的问题，比如令牌撤销，但这不在这里讨论。理解 JWT 有多有用的基础是首先掌握 OAuth 2.0。为了快速参考，下面是 OAuth 舞蹈的插图。

![](img/878f3ce05a6d0d984e4272f5914de46e.png)

OAUTH 2 的插图。跳舞

# **项目需要什么？**

1.Spring Boot 1.5.3 .用 Maven 或 Gradle 发布项目。这个项目使用 Maven。

2.以下依赖关系:

*   `spring-boot-starter-web`
*   `spring-boot-starter-security`
*   `spring-boot-starter-data-jpa`
*   `spring-boot-starter-web`
*   `com.h2database` —存储样本测试数据的 H2 数据库
*   `spring-security-jwt`版本:1.0.7 .发布
*   版本:2.1.0 .发布

# **最终项目结构**

例如，当您从 Github 克隆项目并将其导入到您的 IDE 中时，它的结构将如下所示。这里，我使用了 IntelliJ Community Edition IDE 中的一个截图。根据您使用的 IDE，您的外观会略有不同。

![](img/546732235489e5f7e32b42ff8eee5a37.png)

# 1.配置 Spring 安全性

多亏了 Spring Boot 的自动配置，我们将需要最少的定制来设置:

首先，让我们看看应用程序的配置文件，`application.properties`:

然后是安全配置类:

*   `@EnableWebSecurity`:启用 spring 安全并告诉 Spring Boot 应用所有敏感的默认值
*   `@EnableGlobalMethodSecurity`:允许我们拥有方法级访问控制
*   `JwtTokenStore`和`JwtAccessTokenConverter`bean:授权服务器需要一个`JwtTokenStore` bean，为了使资源服务器能够解码访问令牌，两个服务器都必须使用一个`JwtAccessTokenConverter` bean。在这种情况下，我们提供对称密钥签名。
*   `UserDetailsService`:我们注入一个`UserDetailsService`的自定义实现，名为`AppUserDetailsService`(详见 GitHub 上的代码)，以便从数据库中检索用户详细信息。
*   编码:SHA-256 用于对密码进行编码。这是在`encodingStrength`属性中设置的
*   领域:安全领域名在`securityRealm`属性中定义。这个名字是任意取的。从提供者到角色、用户等，领域基本上就是定义我们的安全解决方案的所有内容。

# 2.配置授权服务器

*   `@EnableAuthorizationServer`:启用授权服务器
*   `AuthorizationServerConfig`是我们的授权服务器配置类，它扩展了`AuthorizationServerConfigurerAdapter`，而`AuthorizationServerConfigurerAdapter`又是`AuthorizationServerConfigurer`的实现。类型为`AuthorizationServerConfigurer`的 bean 的出现只是告诉 Spring Boot 关闭自动配置，使用定制配置。此外，`AuthorizationServerConfig`像任何其他配置类一样，因为有了`@Configuration`注释，它的定义被 Spring Boot 自动扫描、连接和应用。
*   客户机 id:定义被授权进行身份验证的客户机应用程序的 id，客户机应用程序提供这个 id 是为了被允许向服务器发送请求。
*   客户端密码:是客户端应用程序的密码。在一个重要的实现中，客户端 id 和密码将被安全地存储在数据库中，并可通过客户端应用程序在部署期间访问的单独 API 来检索。这些信息也可以共享并存储在环境变量中，尽管这不是我的首选。
*   授权类型:我们在这里定义授权类型密码，因为默认情况下它是不启用的
*   范围:读/写定义了我们允许的对资源的访问级别
*   资源 id:此处指定的资源 Id 也必须在资源服务器上指定
*   AuthenticationManager: Spring 的身份验证管理器负责检查用户凭证的有效性
*   TokenEnhancerChain:我们定义了一个令牌增强器，它能够链接包含不同信息的多种声明

# 3.配置资源服务器

`[@EnableResourceServer](http://twitter.com/EnableResourceServer)`:启用资源服务器。默认情况下，该注释创建一个安全过滤器，该过滤器通过传入的 OAuth2 令牌对请求进行身份验证。过滤器是`WebSecurityConfigurerAdapter`的一个实例，它的硬编码顺序是三(由于 Spring 框架的一些限制)。您需要告诉 Spring Boot 将 OAuth2 请求过滤器顺序设置为 3，以便与硬编码值保持一致。你可以通过在`application.properties`文件中添加`security.oauth2.resource.filter-order = 3`来实现。希望这将在未来的版本中得到解决。

资源服务器有权为任何端点定义权限。端点权限定义为:
`.antMatchers(“/actuator/**”, “/api-docs/**”).permitAll()
.antMatchers(“/springjwt/**”).authenticated()`

请注意，资源和授权服务器都使用相同的令牌服务。这是因为它们在同一个代码库中，所以我们重用了同一个 bean。

# 4.配置数据源

一旦在类路径上定义了内存中的 H2 数据源，Spring Boot 就可以完全自动配置它。但是，为了让您更好地了解如何控制应用程序和定制数据源，提供了以下配置:

用任何数据库(MariaDB，MySQL，Oracle，SQL Server 等)代替 H2。)来适应您的使用情况。

# 5.数据库脚本和测试数据

## `schema.sql`

## data.sql

# 6.实体

创建用户、角色、随机城市实体来映射到数据模型

# 7.通过 REST 控制器公开资源

这里暴露了两个端点

*   `/springjwt/cities`:所有经过身份验证的用户都可以访问该端点
*   `/springjwt/users`:只有管理员用户可以访问该端点

# 8.运行和测试应用程序

首先，您需要以下基本信息:

*   客户:`testjwtclientid`
*   秘密:`XY7kmzoNzl100`
*   非管理员用户名和密码:`john.doe`和`jwtpass`
*   管理员用户:`admin.admin`和`jwtpass`
*   所有认证用户都可以访问的资源示例:[http://localhost:8080/spring jwt/cities](http://localhost:8080/springjwt/cities)
*   只有管理员用户可以访问的资源示例:[http://localhost:8080/spring jwt/users](http://localhost:8080/springjwt/users)

## **步骤 1:生成访问令牌**

使用下面的通用命令生成一个访问令牌:`$ curl client:secret@localhost:8080/oauth/token -d grant_type=password -d username=user -d password=pwd`

对于这个特定的应用程序，要为非管理员用户`john.doe`生成一个访问令牌，运行:`$ curl testjwtclientid:XY7kmzoNzl100@localhost:8080/oauth/token -d grant_type=password -d username=john.doe -d password=jwtpass`。您将收到类似以下内容的回复:

```
`
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsidGVzdGp3dHJlc291cmNlaWQiXSwidXNlcl9uYW1lIjoiYWRtaW4uYWRtaW4iLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNDk0NDU0MjgyLCJhdXRob3JpdGllcyI6WyJTVEFOREFSRF9VU0VSIiwiQURNSU5fVVNFUiJdLCJqdGkiOiIwYmQ4ZTQ1MC03ZjVjLTQ5ZjMtOTFmMC01Nzc1YjdiY2MwMGYiLCJjbGllbnRfaWQiOiJ0ZXN0and0Y2xpZW50aWQifQ.rvEAa4dIz8hT8uxzfjkEJKG982Ree5PdUW17KtFyeec",
  "token_type": "bearer",
  "expires_in": 43199,
  "scope": "read write",
  "jti": "0bd8e450-7f5c-49f3-91f0-5775b7bcc00f"
}`
```

## **步骤 2:使用令牌通过 RESTful API 访问资源**

使用令牌通过 RESTful API 访问资源

要访问所有已验证用户可用的内容:

*   使用生成的令牌作为授权报头中的载体值，如下:`curl http://localhost:8080/springjwt/cities -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsidGVzdGp3dHJlc291cmNlaWQiXSwidXNlcl9uYW1lIjoiYWRtaW4uYWRtaW4iLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNDk0NDU0MjgyLCJhdXRob3JpdGllcyI6WyJTVEFOREFSRF9VU0VSIiwiQURNSU5fVVNFUiJdLCJqdGkiOiIwYmQ4ZTQ1MC03ZjVjLTQ5ZjMtOTFmMC01Nzc1YjdiY2MwMGYiLCJjbGllbnRfaWQiOiJ0ZXN0and0Y2xpZW50aWQifQ.rvEAa4dIz8hT8uxzfjkEJKG982Ree5PdUW17KtFyeec"`
*   响应将是:`[ { "id": 1, "name": "Bamako" }, { "id": 2, "name": "Nonkon" }, { "id": 3, "name": "Houston" }, { "id": 4, "name": "Toronto" }, { "id": 5, "name": "New York" }, { "id": 6, "name": "Mopti" }, { "id": 7, "name": "Koulikoro" }, { "id": 8, "name": "Moscow" } ]`

要访问仅对管理员用户可用的内容:

和前面的例子一样，首先，用上面提供的凭证为 admin 用户生成一个访问令牌，然后运行`curl http://localhost:8080/springjwt/users -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOlsidGVzdGp3dHJlc291cmNlaWQiXSwidXNlcl9uYW1lIjoiYWRtaW4uYWRtaW4iLCJzY29wZSI6WyJyZWFkIiwid3JpdGUiXSwiZXhwIjoxNDk0NDU0OTIzLCJhdXRob3JpdGllcyI6WyJTVEFOREFSRF9VU0VSIiwiQURNSU5fVVNFUiJdLCJqdGkiOiIyMTAzMjRmMS05MTE0LTQ1NGEtODRmMy1hZjUzZmUxNzdjNzIiLCJjbGllbnRfaWQiOiJ0ZXN0and0Y2xpZW50aWQifQ.OuprVlyNnKuLkoQmP8shP38G3Hje91GBhu4E0HD2Fes"`

结果将是:`[ { "id": 1, "username": "john.doe", "firstName": "John", "lastName": "Doe", "roles": [ { "id": 1, "roleName": "STANDARD_USER", "description": "Standard User - Has no admin rights" } ] }, { "id": 2, "username": "admin.admin", "firstName": "Admin", "lastName": "Admin", "roles": [ { "id": 1, "roleName": "STANDARD_USER", "description": "Standard User - Has no admin rights" }, { "id": 2, "roleName": "ADMIN_USER", "description": "Admin User - Has permission to perform admin tasks" } ] } ]`

# **9。访问不安全或不受保护的端点**

[http://localhost:8080/health](http://localhost:/health)不受限制。匿名用户可以访问它，并且可以将其用于负载平衡健康检查目的。

# 参考资料和有用的阅读材料:

*   Spring Boot 1.5.3.RELEASE 转到[http://docs . spring . io/spring-boot/docs/1 . 5 . 3 . release/reference/html single/](http://docs.spring.io/spring-boot/docs/1.5.3.RELEASE/reference/htmlsingle/)了解更多关于 Spring Boot 的信息
*   JSON Web Token 去[https://jwt.io/](https://jwt.io/)解码你生成的令牌并了解更多
*   H2 数据库引擎——用于快速原型开发，但至少在大多数情况下不适合生产。前往 www.h2database.com[了解更多信息](http://www.h2database.com/)
*   为什么需要在 application.properties 文件**中设置**security . oauth 2 . resource . filter-order = 3**:**[https://docs . spring . io/spring-security/oauth/API docs/org/spring framework/security/oauth 2/config/annotation/web/configuration/enableresourceserver . html](https://docs.spring.io/spring-security/oauth/apidocs/org/springframework/security/oauth2/config/annotation/web/configuration/EnableResourceServer.html)