# 使用 Mockito 在 Flutter 上对 Appwrite SDK 进行单元测试的简单步骤

> 原文：<https://betterprogramming.pub/write-unit-test-for-appwrite-sdk-on-flutter-with-mockito-e0c3b403199e>

## 用 Mockito 为 Appwrite SDK 编写单元测试的实用指南

![](img/dbb8c37626404d5c4c1be80db1e97089.png)

[Appwrite](https://appwrite.io/) 是一个开源(OSS)后端服务，旨在抽象构建现代应用程序所需的常见、复杂和重复任务的复杂性。Appwrite 可以作为 Firebase 的替代产品，具有一系列功能，如身份验证、数据库、存储、功能和实时支持。此外，它提供了几乎所有平台的 SDK，如 web、Android、iOS、Flutter 等。

虽然我们使用 SDK 开发一个应用程序。单元测试对于测试我们的应用程序是否很好地连接到 SDK 仍然很重要。因此，在本文中，我想向您展示当使用 Appwrite 作为后端 SDK 时，如何在您的 Flutter 项目上创建一个单元测试。

如果你是术语单元测试的新手，你可以在这里阅读我之前关于[颤振测试的文章](/flutter-testing-101-understand-unit-widget-and-integration-testing-fd5d07e312e)。

## 步骤 1 —获取 Mockito 插件

因为在单元测试中，我们不执行真正的函数。因此，为了能够对像 Appwrite 这样的服务进行单元测试，我们需要为该服务创建一个模拟。为此，我们需要一个名为 Mockito 的插件

[](https://pub.dev/packages/mockito) [## 莫奇托|镖包

### 受 Mockito 启发的 Dart 模拟库。Mockito 5.0.0 支持 Dart 2.12 中 Dart 新的空安全语言特性…

公共开发](https://pub.dev/packages/mockito) 

让我们将`mockito`添加到`pubspec.yaml`文件的`dev_dependencies`中。

```
...
dev_dependencies:
    flutter_test:
        sdk: flutter build_runner: ^2.1.7
    mockito: ^5.1.0
...
```

除了`mockito`，我们还需要`build_runner`它，因为 mockito 将生成模拟文件。

[](https://pub.dev/packages/build_runner) [## build_runner | Dart 包

### 使用包:build 的 Dart 的独立生成器和观察器。build_runner 包提供了一种具体的方法…

公共开发](https://pub.dev/packages/build_runner) 

## 步骤 2 —创建 Appwrite 模拟

得到我们需要的插件后，下一步是生成我们服务的模拟。例如，我想创建一个用于认证的存储库。因此，我将从 Appwrite SDK 中为`Account`生成一个模拟。

为了生成一个新的模拟文件，我们在测试文件上使用了`@GenerateMocks()`注释。之后，我们需要运行`build_runner`来开始生成模拟文件。该命令将生成一个类似`auth_repository_test.mocks.dart`的新文件

```
flutter packages pub run build_runner build
```

## 步骤 3 —编写测试

现在，我们已经准备好编写测试了。作为一个例子，我将为登录函数编写一个测试。下面是我的`auth_repository_test.dart`测试文件的样子:

`MockAccount`取自我们之前生成的模拟文件。在`AuthRepositoryImpl`上，我将`_mockAccount`作为依赖项传递。

## 步骤 4 —编写实际代码

最后一步是编写实际的存储库代码。下面是我的`auth_repository.dart`文件的样子:

对了，Appwrite 还提供了`AppwriteException`类。如果你想为你的应用程序创建更丰富的错误处理，这是很有帮助的。例如，密码错误警告、电子邮件已被使用警告等。

因此，当你使用 Appwrite 作为 SDK 来简化你的生活，同时单独创建一个应用程序时，这是一步一步为你的 Flutter 项目编写单元测试。当编写单元测试时，我使用测试驱动开发方法。如果你对 TDD 感兴趣，你可以在这里阅读更多内容:

[](/flutter-clean-architecture-test-driven-development-practical-guide-445f388e8604) [## 通过实现干净的架构和测试驱动，增强你的颤振开发过程…

### 在 Flutter 代码库中建立干净架构的完整过程

better 编程. pub](/flutter-clean-architecture-test-driven-development-practical-guide-445f388e8604) 

如果您有任何问题，请随时开始讨论。

感谢阅读。