# 科特林尝试让你的代码更有弹性

> 原文：<https://betterprogramming.pub/kotlin-retry-to-make-your-code-more-resilient-5b8cc4fac4e4>

## Kotlin 包装函数重试代码的任何部分

![](img/542abfce35b8b8ef9df452caf53c3fab.png)

这是我的“Kotlin cookbook”函数之一，当我需要对代码的任何部分进行简单的重试时，它会有所变化。在标准的 Kotlin 内联函数中，我没有发现任何类似的东西，所以我试图发明自己的函数。

# 最简单的实现

众所周知，当使用远程资源时，“重试模式”被认为是最佳实践，并且是[弹性设计](https://blog.codecentric.de/en/2019/06/resilience-design-patterns-retry-fallback-timeout-circuit-breaker/)所必须的。因此，当我需要一个简单而优雅的重试时，我将这个内联函数引入我的源代码:

```
inline fun <T> executeWithSocketTimeoutRetry(call: () -> T) =
    try {
        call()
    } catch (e: ProcessingException) {
        call()
    }
```

> 请记住，在大多数 WebClient 实现中，您无法直接捕获" Java . net . sockettimeoutexception:Read timed "**，*，这样做是一个常见的错误*

*相反，您需要捕捉一个特定的[包装器](https://stackoverflow.com/questions/37244264/java-jersey-retry-request-on-sockettimeoutexception)异常。当然，您可以捕捉一般的异常，但不建议这样做。*

## *使用*

```
*val result = *executeWithSocketTimeoutRetry* {//do request and return a result
}*
```

*下面是如何在由 [Jersey REST 框架](https://github.com/eclipse-ee4j/jersey)抛出的 SocketTimeoutException(Read timed out)上用 retry 包装一部分代码，并从中获得一个结果。*

# *改进实施*

*我想你已经明白了，为了让事情更明显，下面是上面“重试”功能的另一个更有用的实现。*

*这里我声明了一种我想要捕捉的异常。这使得这个函数更具可重用性。由于语言的限制，我不能直接捕捉参数化的异常。一种解决方法是捕获所有异常，然后用条件表达式检查它们。*

## *使用*

```
**executeWithRetry*<IllegalArgumentException> {//some important code here
}*
```

*在这个变体中，我正在重试代码的一部分，并且不期望任何输出。*

# *高级实施*

*但是如果您需要从包装的代码中返回一些值呢？这也是可能的，但是将异常类型移到参数列表会更容易，因为 Kotlin 不能自动推断多个泛型类型。既然我们现在已经有了参数，让我们带来一个更方便的参数—重试次数。*

## *使用*

*这就是我们如何在 Kotlin 中简单地重试代码的任何部分。*

```
*val result = executeWithRetry{
   // code that returns value
}*
```

*或者更具体地说，仅在`IllegalArgumentException`重试。*

```
*val result = executeWithRetry({ it is IllegalArgumentException }) {
   // code that returns value
}*
```

*使用谓词在灵活性方面给了我们很多优势。例如，我们可以在解开原始异常时捕捉一些异常类型。*

```
*val result = *executeWithRetry*({ it is IllegalArgumentException || it.cause is IOException}) {
    // code that returns value
}*
```

*我们甚至可以在每次重试后做额外的动作，比如用`also`关键字记录重试次数。*

```
*val result = *executeWithRetry*({ e ->
    (e is IOException).*also* { if (it) *println*("Retrying after $e") }
}) {
    // code that returns value
}*
```

*或者甚至重试不止一次，重试之间有延迟。*

```
*val result = *executeWithRetry*({ e ->
    (e is IOException).*also* { if (it) Thread.sleep(100) }
}, retries = 2) {
    // code that returns value
}*
```

# *额外的 Java 实现*

*如果您对 Java 实现感兴趣，我可以满足您。在 Kotlin 之前，我使用了与 Java 8 相同的思想，用额外的逻辑包装代码的一部分。*

*为了方便起见，我将所有的`Exceptions`用`RuntimeExceptions`包装起来。*

```
*var restult = executeWithRetry(IllegalArgumentException.class, () -> {
    //code that returns result
});*
```

# *结论*

*这就是你如何在没有任何额外的库或代码依赖的情况下重试你的部分代码。您还可以将这种包装器思想用于其他功能，如日志记录、前/后逻辑等。一些标准的 Kotlin 内联函数使用相同的方法，例如`[measureTime](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.time/measure-time.html)`、**、**。*

*当然，这是最简单的重试实现。一旦我需要更复杂的东西，我就用 [Resilience4j](https://github.com/resilience4j/resilience4j) 库，因为它给了我其他现成的特性，比如用`ExponentialBackoff`和`CircuitBreaker`重试。*