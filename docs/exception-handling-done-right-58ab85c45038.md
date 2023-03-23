# 用 Kotlin 处理 Spring REST 服务中的错误

> 原文：<https://betterprogramming.pub/exception-handling-done-right-58ab85c45038>

## 异常处理做对了！

![](img/48d9de67dc0fb49d6f904eccd947ff46.png)

照片由[中村太极](https://unsplash.com/@naka_mura?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

本文将展示如何在 Spring web 服务中实现异常处理。我们将带您浏览 Spring 中处理错误场景的选项，并为每个选项返回一个确定的响应。

我们还将介绍另一种实现错误处理的基本方法，这种方法不需要 Spring 组件。

开始吧！

# 介绍

REST 服务中的错误处理一直有点争议。主要原因是可以采用不同的方法，每种方法都有自己的优点和缺点。事实上，在某些情况下，每一种观点的利弊并不完全客观，这无助于解决这些争论。

我们将带你通过不同的方式来处理春假服务中的错误场景，尽量做到客观。我们的目标是让您判断并决定哪种方法最适合您。

在我们进入处理异常的可能方法之前，让我们为我们的前几个例子设置基础。

在我们的前几个例子中，我们将使用一个假的`CustomersRepository`，它使用以下接口:

```
interface CustomersRepository {
    /**
     * Creates a customer and returns the customer including the generated ID
     */
    fun create(customer: Customer): Customer
    /**
     * Finds an existing customer or returns CustomerNotFoundException
     */
    fun findById(id: Long): Customer
}
```

你可以看到在这两种情况下我们是如何返回一个`Customer`的，如果事情没有按预期进行会发生什么？我们将抛出一个异常并相应地处理它。

如果您需要帮助来引导一个简单的 Spring Boot 应用程序来运行这些例子，您可以查看我们的文章[“如何引导一个 Spring Boot 应用程序](https://theboreddev.com/how-to-bootstrap-a-spring-boot-application/)”

开始吧！

![](img/e3d112a19fe3b566e37d48dd0ffb0b7e.png)

照片由 [Soheb Zaidi](https://unsplash.com/@msohebzaidi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

# 基于控制器的异常处理

在 Spring 中处理异常的一个最基本的选项是在控制器级别使用`@ExceptionHandler`注释。

我们如何做到这一点？我们来看一个有两个基本端点的`CustomerController`。我们将添加一个方法来处理没有找到客户的情况。它看起来像这样:

```
@RestController
@RequestMapping("/api/customers")
class CustomerController(@Autowired val repository: CustomersRepository) {
    @PostMapping
    fun createCustomer(@RequestBody customer: Customer, uriBuilder: UriComponentsBuilder): ResponseEntity<String> {
        val result = repository.create(customer)
        return ResponseEntity
           .created(uriBuilder.path("/api/customers/{id})").buildAndExpand(result.id).toUri())
            .build()
    }
    @GetMapping("/{id}")
    fun findCustomer(@PathVariable("id") id: Long): ResponseEntity<Customer> {
        val result = repository.findById(id)
        return ResponseEntity.ok(result)
    }
    @ExceptionHandler(CustomerNotFound::class)
    fun customerNotFound(exception: CustomerNotFound): ResponseEntity<String> {
        return ResponseEntity.notFound().build()
    }
}
```

在这种情况下，如果客户不在我们的数据库中，我们的存储库将返回`CustomerNotFound`异常。该异常先前已经以这种方式声明:

```
data class CustomerNotFound(override val message: String? = null, val id: Long): Exception(message)
```

为了在应用程序冒泡时处理这个异常，我们必须在控制器中定义一个处理程序。我们通过使用`@ExceptionHandler`注释来做到这一点。

通过使用`@ExceptionHandler`，我们将自定义响应映射到给定的异常。在这种情况下，给出的异常是`CustomerNotFound`。

还有另一种方法可以做类似的事情。这是通过使用`@ResponseStatus`注释。

`@ResponseStatus`注释将我们的响应映射到相应的状态代码，所以在我们的例子中，它相当于返回`ResponseEntity.notFound()`。

在这种情况下，我们的异常处理程序方法会略有不同:

```
@ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Customer could not be found!")
@ExceptionHandler(CustomerNotFound::class)
fun customerNotFound(exception: CustomerNotFound) {}
```

这种方法的一些优点是，就可读性或可维护性而言，很容易发现在当前控制器中如何处理异常，以及当异常发生时配置的响应。这也是在我们的代码中定义异常处理的简洁方法。

另一方面，使用基于控制器的异常处理不允许重用这些处理程序，甚至不允许配置应用于所有控制器的全局异常处理程序。

现在让我们看看另一个选项。

# 使用@ControllerAdvice

定义异常处理程序的另一种方式是使用`@ControllerAdvice`注释。这将允许我们在应用程序中定义全局异常处理。让我们看看它会是什么样子:

```
@ControllerAdvice
class GlobalExceptionHandler {
    @ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Customer could not be found!")
    @ExceptionHandler(CustomerNotFound::class)
    fun customerNotFound(exception: CustomerNotFound) {}
}
```

如果我们尝试使用 curl 获取一个不存在的客户，我们也会得到一个 404，就像我们看到基于控制器的异常处理时一样。

```
curl -v http://localhost:8080/api/customers/23                                                                                                                                                                                      
*   Trying 127.0.0.1:8080...
* Connected to localhost (127.0.0.1) port 8080 (#0)
> GET /api/customers/23 HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.79.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 404
< Content-Type: application/json
< Transfer-Encoding: chunked
< Date: Tue, 29 Nov 2022 07:23:16 GMT
<
{"timestamp":"2022-11-29T07:23:16.265+00:00","status":404,"error":"Not Found","trace":"CustomerNotFound(message=Could not find customer 23, id=23)
...
```

这可能是 Spring Boot 定义异常处理最常用的方法。它的主要优点是我们不必在每个控制器中定义一个异常处理程序。我们必须在代码中定义一次，每个控制器都会重用它。

另一方面，主要的缺点是，为了能够知道应用程序在哪里配置异常处理，我们需要很好地理解 Spring，并且我们必须搜索`@ControllerAdvice`来找到它被配置的位置。因此，就可读性而言，对于不熟悉代码或 Spring 的人来说，这种方法并不是最好的。

还有一件事我们之前没有提到，那就是 Spring 如何在内部处理这两种方法。如果我们查看我们的日志，我们将能够看到类似这样的内容:

```
2022-11-29 07:29:56.196  WARN 36520 --- [nio-8080-exec-2] .m.m.a.ExceptionHandlerExceptionResolver : Resolved [CustomerNotFound(message=Could not find customer 123, id=123)]
```

如您所见，Spring 调用`ExceptionHandlerExceptionResolver`来解决配置的异常。让我们多了解一下这个组件。

# ExceptionHandlerExceptionResolver

该组件负责解决通过`@ExceptionHandler`注释定义的所有异常。因此，它是 Spring 异常处理中非常重要的组件！

这个组件也是从另一个已知组件`[AbstractHandlerMethodExceptionResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/handler/AbstractHandlerMethodExceptionResolver.html)`扩展而来的。该组件从`[HandlerExceptionResolver](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/HandlerExceptionResolver.html)`接口扩展而来，这允许我们定义自己的自定义异常解析器。

这种方法不常用，因为现在有更好的方法。然而，如果你仍然想看一个实现定制异常解析器的例子，你可以在这里找到它。

我们已经看到了 Spring 提供的处理异常的主要方法，但是还有其他方法吗？我们认为还有一种方式，像往常一样，有利有弊。让我们看看如何！

# 使用单子的自定义方式

一些观点认为春天“太神奇了”因此，我们不知道在我们的应用程序中到底发生了什么。对于站在这一边的人，我们还有一个提议。开始吧！

## 引入基于控制器的处理

我们要做的第一件事是改变我们的`CustomerRepository`接口，返回一个`Either`单子。

在这个例子中我们也可以使用 Arrow，但是我们决定为这个例子创建一个自定义的`Either`类。看起来是这样的:

```
sealed class Either<F, S> {

    data class Success<F, S>(private val s: S) : Either<F, S>() {
        fun entity(): S = s
    }
    data class Failure<F, S>(private val f: F) : Either<F, S>() {
        fun exception(): F = f
    }
}
fun <E> E.failure() = Either.Failure<E, Nothing>(this)
fun <T> T.success() = Either.Success<Nothing, T>(this)
```

如你所见，这很简单。我们将通过一个例子来看看如何使用它。

首先，我们将修改一个定制的`InMemoryCustomerRepository`实现来使用我们的`Either`类。

```
@Component("inMemoryCostumersRepository")
class InMemoryCustomerRepository(val configuration: DatabaseConfiguration): CustomersRepository {
    private val customers = mutableMapOf<Long, Customer>()
    override fun create(customer: Customer): Either<Exception, Customer> {
        val created = customer.copy(id = customers.keys.size.toLong())
        customers[created.id!!] = created
        return created.success() as Either<Exception, Customer>
    }
    override fun findById(id: Long): Either<Exception, Customer> {
        return ((
                customers[id]?.success() ?: 
                    CustomerNotFound("Could not found customer $id", id).failure()
                ) as Either<Exception, Customer>)
    }
}
```

我们将替换任何负责异常处理的基于 Spring 的配置，并自己定义响应。让我们看看在我们的控制器中应用这些更改后会是什么样子:

```
@RestController
@RequestMapping("/api/customers")
class CustomerController(@Autowired val repository: CustomersRepository) {
    @PostMapping
    fun createCustomer(@RequestBody customer: Customer, uriBuilder: UriComponentsBuilder): ResponseEntity<String> {
        return when (val result = repository.create(customer)) {
            is Either.Success -> {
                ResponseEntity
                    .created(uriBuilder
                        .path("/api/customers/{id})")
                        .buildAndExpand(result.entity().id)
                        .toUri()
                    ).build()
            }
            is Either.Failure -> {
                ResponseEntity.internalServerError().build()
            }
        }
    }
    @GetMapping("/{id}")
    fun findCustomer(@PathVariable("id") id: Long): ResponseEntity<Customer> {
        return when (val result = repository.findById(id)) {
            is Either.Success -> ResponseEntity.ok(result.entity())
            is Either.Failure -> {
                when (result.exception()) {
                    is CustomerNotFound -> ResponseEntity.notFound().build()
                    else -> ResponseEntity.internalServerError().build()
                }
            }
        }
    }
}
```

这种方法的主要好处是我们不依赖于冒泡异常。异常处理在我们的代码中，所以我们可以很容易地阅读和理解它的行为。

正如我们前面看到的 Spring 中基于控制器的异常处理，这种方法也有同样的缺点，我们不能在所有的控制器中重用它，我们必须在每个端点中定义它。我们能做些什么来解决这个问题吗？我们试试吧！

## 全局异常处理，使用

我们将尝试修改我们的`Either`类，使其可用于“全局”异常处理。目标是为我们的响应提供一个“默认”配置，但同时，我们可以在控制器级别覆盖行为。

产生的`Either`类将如下所示:

```
sealed class Either<F: Exception, S: Any> {
    protected var successHandler: (Success<F, S>) -> ResponseEntity<S> = { 
            e -> ResponseEntity.ok(e.entity()) 
    }
    protected var errorHandler: (Failure<F, S>) -> ResponseEntity<F> = { 
            e-> ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.exception())
    }
    data class Success<F: Exception, S: Any>(private val s: S) : Either<F, S>() {
        fun entity(): S = s
        override fun response(): ResponseEntity<S> = this.successHandler.invoke(this)
    }
    data class Failure<F: Exception, S: Any>(private val f: F) : Either<F, S>() {
        fun exception(): F = f
        override fun response(): ResponseEntity<F> = this.errorHandler.invoke(this)
    }
    abstract fun response(): ResponseEntity<out Any>
    fun onFailureDo(action: (e: Failure<F, S>) -> ResponseEntity<F>): Either<F, S> {
        this.errorHandler = action
        return this
    }
    fun onSuccessDo(action: (e: Success<F, S>) -> ResponseEntity<S>): Either<F, S> {
        this.successHandler = action
        return this
    }
}
fun <E: Exception> E.failure() = Either.Failure<E, Nothing>(this)
fun <T : Any> T.success() = Either.Success<Nothing, T>(this)
```

在我们的新实现中，有几件事情需要强调。我们将一步一步地看它们，让你更容易理解。

首先要注意的是，我们现在提供了一个`response`方法，能够将我们的`Either`类转换成一个 Spring `ResponseEntity`对象。

```
abstract fun response(): ResponseEntity<out Any>
```

每个`Either`子类`Success`和`Failure`都将覆盖这个方法，将我们的方法映射到相应的 HTTP 响应，包括状态代码和是否有主体。

```
data class Success<F: Exception, S: Any>(private val s: S) : Either<F, S>() {
    fun entity(): S = s
        override fun response(): ResponseEntity<S> = this.successHandler.invoke(this)
    }
    data class Failure<F: Exception, S: Any>(private val f: F) : Either<F, S>() {
        fun exception(): F = f
        override fun response(): ResponseEntity<F> = this.errorHandler.invoke(this)
    }
```

您现在会注意到这两个类调用了两个不同的处理程序，而不是直接返回一个`ResponseEntity`对象。这是为什么呢？嗯，我们决定这样做是为了能够提供一个默认响应，但也允许在我们的控制器中覆盖默认响应。

您现在可能想知道，默认配置是什么？

我们用默认配置初始化`successHandler`和`errorHandler`，如下所示:

```
protected var successHandler: (Success<F, S>) -> ResponseEntity<S> = {
     e -> ResponseEntity.ok(e.entity())
}
protected var errorHandler: (Failure<F, S>) -> ResponseEntity<F> = {
     e-> ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(e.exception())
}
```

如您所见，任何成功的响应都将被转换为 200 (OK)响应，包括主体中的实体(如果存在的话)。另一方面，任何失败都将被默认转换为 500(服务器错误)响应，包括主体中的异常。

这只是一个简单的例子。你自己决定万一失败要在正文中包含什么。例如，您可以将消息包含在异常中。

既然我们已经了解了默认行为是如何设置的，那么我们如何在特定的情况下覆盖这种行为呢？为此，我们提供了以下两种方法:

```
fun onFailureDo(action: (e: Failure<F, S>) -> ResponseEntity<F>): Either<F, S> {
  this.errorHandler = action
  return this
}
fun onSuccessDo(action: (e: Success<F, S>) -> ResponseEntity<S>): Either<F, S> {
  this.successHandler = action
  return this
}
```

您可以看到这些方法是如何接受一个接收`Either.Success`或`Either.Failure`的函数，并返回一个带有相应失败或成功类型的`ResponseEntity`对象的。

那么，我们如何利用所有这些呢？整合后，让我们看看我们的新`CustomerController`:

```
@RestController
@RequestMapping("/api/customers")
class CustomerController(@Autowired val repository: CustomersRepository) {
    @PostMapping
    fun createCustomer(@RequestBody customer: Customer, uri: UriComponentsBuilder): ResponseEntity<String> {
        return repository.create(customer)
            .onSuccessDo { e ->
                ResponseEntity
                    .created(uri
                        .path("/api/customers/{id})")
                        .buildAndExpand(e.entity().id)
                        .toUri()
                    ).build()
            }.response() as ResponseEntity<String>
    }
    @GetMapping("/{id}")
    fun findCustomer(@PathVariable("id") id: Long): ResponseEntity<Customer> {
        return repository.findById(id)
            .onFailureDo { e ->
                when (e.exception()) {
                    is CustomerNotFound -> ResponseEntity.notFound().build()
                    else -> ResponseEntity.internalServerError().build()
                }
            }.response() as ResponseEntity<Customer>
    }
}
```

您可以看到我们的控制器现在如何使用默认配置来处理`createCustomer`方法中的失败和`findCustomer`方法中的成功。例如，`findCustomer`将返回一个 200 (OK)响应，并将客户包含在 JSON 主体中。

然而，我们覆盖了在`createCustomer`方法中成功的行为和在`findCustomer`方法中失败的行为。这是为什么呢？在我们的例子中，成功创建一个客户意味着返回 201 (CREATED)和一个`Location`头，正如我们在本文前面看到的。我们还需要对`findCustomer`中的故障采取不同的行为，以便能够正确处理`CustomerNotFound`。

很好，对吧？这种方法的主要优点是，我们现在能够为异常处理提供全局配置，同时能够容易地阅读和理解在我们的代码中如何配置异常处理，而不涉及 Spring“魔法”！

![](img/c70cbce066715ebadf1e37e1d6b1a176.png)

图片由 [Aziz Acharki 在 Unsplash 上拍摄](https://unsplash.com/photos/PUvPZckRnOg)

如果你喜欢我们的解决方案，并且对 Java 版本感兴趣，你可以在这里查看我们的文章和 Java 示例。

# 结论

在本文中，我们看到了在 Spring Boot 应用程序中处理异常的不同方法。正如我们之前所说的，异常处理是一个非常有争议的话题，因为对于什么方法在我们的应用程序中最有效有不同的意见。希望我们已经能够对这个话题提出一个客观公正的观点，现在你可以用它来做出你自己的假设，并在你的项目中做出正确的决定。

这就是我们今天的全部内容！我们真的希望你喜欢这篇文章，就像我们喜欢写它一样！

期待很快再次见到您！

感谢阅读！