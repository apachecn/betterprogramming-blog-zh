# 在 Flutter 中测试流需要知道的两件事

> 原文：<https://betterprogramming.pub/2-things-you-really-need-to-know-to-test-streams-in-flutter-937b71b82abe>

## 流匹配器和 Q

![](img/cba6e6928a080d1c8c02be6e95d14bbb.png)

流是 Dart 的一大特色，许多 Flutter 应用程序都利用了它们。但是如果你真的在你的 Flutter 应用中使用它们，你也会想要测试它们，为此，我在这里提出了我发现在测试流中必须知道的两件事。

# 1.流匹配器

第一件事是在测试中使用流匹配器。您可能已经熟悉在测试中使用匹配器，例如:

```
import 'package:test/test.dart';void main() {
  test('basic test', () async {
    const lifeTheUniverseAndEverything = 21 + 21;
    expect(lifeTheUniverseAndEverything, equals(42));
  });
}
```

在上面的代码中，`equals(42)`的值返回一个匹配器。

那么我们如何对流使用匹配器呢？例如，如果我们有一个发出单个 int 值的非常基本的流，我们可能想要测试它实际上发出了我们期望的 int 值，为此，我们可以使用`[emitsInOrder()](https://api.flutter.dev/flutter/package-test_api_expect/emitsInOrder.html)` [函数](https://api.flutter.dev/flutter/package-test_api_expect/emitsInOrder.html)来得到一个`[StreamMatcher](https://api.flutter.dev/flutter/package-test_api_expect/StreamMatcher-class.html)`。

```
test('test a stream emits expected item', () async {
    final testSubject = StreamController<int>();testSubject.add(1);expect(testSubject.stream, emitsInOrder([1]));
  });
}
```

是的，如果我们运行它，我们就通过了测试。

作为 StreamMatcher 说明文档中的示例代码，我们实际上可以进行更复杂的匹配:

```
test('test a stream emits expected items in expected order', () async {
    final testSubject = StreamController<int>();testSubject.add(1);
    testSubject.add(2);
    testSubject.add(3);
    testSubject.close();expect(
      testSubject.stream,
      emitsInOrder(
        [
          1,
          emitsAnyOf([0, 2]),
          lessThanOrEqualTo(3),
          emitsDone,
        ],
      ),
    );
  });
```

我强烈推荐阅读 Andrea 的优秀文章,这篇文章对使用匹配器测试流有更详细的解释。

# 2.Q

当谈到测试流时，我的第二个重要技巧是如何测试监听流的代码。假设我们有一个类，它将流作为依赖项，然后对流事件进行一些处理，然后可能基于此改变其状态。更具体地说，我们可能有:

```
class Marvin {
  final Stream<int> answerStream; bool isHappy = false; Marvin(this.answerStream) {
    answerStream.listen((event) {
      isHappy = (event == 42);
    });
  }
}
```

因此我们可以设计一个测试:

```
test('test Marvin is happy with the correct answer', () async {
    final fakeStream = StreamController<int>();
    final testMarvin = Marvin(fakeStream.stream); fakeStream.add(42); expect(testMarvin.isHappy, true);
  });
```

但遗憾的是，当我们运行上述测试时，我们得到了:

```
Expected: <true>
  Actual: <false>package:test_api                          expect
main.<fn>
test/dart_streams_testing_test.dart:46
2✖ test Marvin is happy with the correct answer
```

哪里出了问题？

嗯，我们忘记了考虑 Dart 流是如何处理的，以及它们的事件是如何传递给侦听器的。虽然没有明确说明，但关于 Dart 中异步编程的优秀文章说:

> 【Dart 为异步编程提供的所有高级 API 和语言特性——futures、streams、async 和 await——都建立在这个简单的循环之上。

上面的结果是流事件被实际处理，也就是通过事件循环传递给它们的监听器。因此，如果我们想要测试由我们的`testMarvin`监听器添加到我们的测试流中的项目的交付，我们需要允许事件循环完成，从而处理在循环的每一个循环结束时发生的流事件。那么我们如何做到这一点呢？对我们来说幸运的是，Dart 团队领先我们一步，并且已经用`[pumpEventQueue()](https://pub.dev/documentation/test_api/latest/hooks/pumpEventQueue.html)` [功能](https://pub.dev/documentation/test_api/latest/hooks/pumpEventQueue.html)为这种非常偶然的情况做好了准备。所以在我们的测试中增加了这个:

```
test('test Marvin is happy with the correct answer', () async {
    final fakeStream = StreamController<int>();
    final testMarvin = Marvin(fakeStream.stream); fakeStream.add(42); await pumpEventQueue(); expect(testMarvin.isHappy, true);
  });
```

我们的测试如期通过。

# 继续前进。

正如你所看到的，在 Dart 中测试流并因此测试你的 Flutter 应用并不是一件困难的任务，只要你知道这两个非常有用的知识。

编码快乐！

【https://manichord.com】最初发表于[](https://manichord.com/blog/posts/test-streams-in-flutter.html)**。**