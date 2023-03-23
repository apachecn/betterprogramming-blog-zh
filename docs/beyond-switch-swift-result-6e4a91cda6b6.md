# Swift:超越打开结果

> 原文：<https://betterprogramming.pub/beyond-switch-swift-result-6e4a91cda6b6>

## **雨燕。Result** 的潜力不仅仅是使用 switch 子句获得值/错误。让我们看看你能做的几件事

![](img/4eaf952d88b72f433e640173b4a17145.png)

照片由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 获取成功值作为可选值

在某些情况下，你只需要一个`Result`的成功值。有了`func get() throws -> Success`，就不需要使用 switch 子句了。

```
let resultSuccess = Result<Int, Error>.success(1)
let value = try? resultSuccess.get()
// value is Optional(1)let resultFailure = Result<Int, Error>.failure(someError)
let value2 = try? resultFailure.get()
// value is nil
```

# 将抛出表达式转换为结果

还有其他情况，当你有一个抛出表达式(例如函数、闭包……)但需要一个`Result`来代替。今天是你的幸运日，因为`Result`有一个`init`捕捉抛出的错误。

`init(catching body: () throws -> Success)`

```
enum RandomError: Error {
  case noNumber
}func random() throws -> UInt {
  if Bool.random() {
    return UInt.random(in: 1...20)
  } else {
    throw RandomError.noNumber
  }
}let randomResult: Result<UInt, Error> = Result { try random() }
```

# 映射成功类型

有时候你从一个函数中得到一个`Result<A, E>`，但是你需要一个`Result<B, E>`。你可以用一个开关来做，但是与你用`Result` map 操作所能做的相比，那要多很多代码:

`func map<NewSuccess>(_ transform: (Success) -> NewSuccess) -> Result<NewSuccess, Failure>`

```
let resultSuccess = Result<Int, Error>.success(1)let stringResult: Result<String, Error> = resultSuccess.map { String($0) }
```

# 映射故障类型

同样，有时你得到了一个`Result<A, E1>`，但需要一个`Result<A, E2>`。所以，你猜怎么着，还有另外一个地图操作:

`func mapError<NewFailure>(_ transform: (Failure) -> NewFailure) -> Result<Success, NewFailure> where NewFailure : Error`

```
enum CustomError: Error { case customErrorA }let customErrorResult = Result<Int, CustomError>.failure(.customErrorA)let errorResult: Result<Int, Error> = customErrorResult.mapError { $0 }
```

# 平面图成功类型

假设您使用一个函数将一个`Result`返回到一个`Result.map`。你会得到一个漂亮的`Result<Result<Success, Failure>, Failure>`。然而，尽管它很漂亮，你可能想要一个简单的`Result<Success, Failure>`来代替。这时你可以使用`flatMap`:

`func flatMap<NewSuccess>(_ transform: (Success) -> Result<NewSuccess, Failure>) -> Result<NewSuccess, Failure>`

```
enum FooError: Error {
  case noNumber, outOfRange
}func random() -> Result<UInt, FooError> {
  if Bool.random() {
    return .success(UInt.random(in: 1...25))
  } else {
    return .failure(FooError.noNumber)
  }
}func factorial(input: UInt) -> Result<UInt, FooError> {
  if input == 0 || input > 20 {
    return .failure(.outOfRange)
  } else {
    let f = (2…input).reduce(1) { $0 * $1 }
    return .success(f)
  }
}// Result<Result<UInt, FooError>, FooError> 👎
let mappedResult: Result<Result<UInt, FooError>, FooError> = random().map { factorial(input: $0) }// Result<UInt, FooError> 👍
let flattenResult: Result<UInt, FooError> = random().flatMap { factorial(input: $0) }
```

# 平面图故障类型

`Result`提供另一种更难应用的操作。假设您想从一个`Result.failure`中恢复，保持成功类型但改变失败类型。那么你可能会发现`Result.flatMap`很有用。

`func flatMapError<NewFailure>(_ transform: (Failure) -> Result<Success, NewFailure>) -> Result<Success, NewFailure> where NewFailure : Error`

```
enum RecoverableError: Error {
  case fooError
}func recoverableOperation() -> Result<Int, RecoverableError> {
  return .failure(.fooError)
}enum RecoveryError: Error {
  case notRecovered
}func recoverFromRecoverableError(_ recoverableError: RecoverableError) -> Result<Int, RecoveryError> {
  return .failure(.notRecovered)
}// Result<Int, RecoveryError>
let flattenErrorResult = recoverableOperation().flatMapError { recoverFromRecoverableError($0) }
```