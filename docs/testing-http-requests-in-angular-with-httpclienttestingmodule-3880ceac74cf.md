# 用 HttpClientTestingModule 测试 Angular 中的 HTTP 请求

> 原文：<https://betterprogramming.pub/testing-http-requests-in-angular-with-httpclienttestingmodule-3880ceac74cf>

![](img/5b0ee0a80f1ed9ce32f0a784fd28ef9f.png)

伊恩·杜利在 [Unsplash](https://unsplash.com/search/photos/sky?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在这篇简短的文章中，我将向您展示如何在 Angular 中测试您的 HTTP 请求，使用来自**@ Angular/common/HTTP/testing 的 **HttpClientTestingModule** 和**HttpTestingController**。**

让我们从课程服务的基本测试设置开始:

我们课程服务的基本测试设置

我希望此服务允许我们执行两个不同的请求:

1.  一个**帖子请求**添加属于特定主题的新课程。
2.  一个 **GET 请求**获取属于特定主题的所有课程。

现在，带着测试优先的心态，让我们为第一种情况编写一个测试:

#为课程服务添加课程测试—发布请求

让我们分解这个测试。这基本上是我们想要实现的目标，也是我们必须在服务中实施的目标:

*   我们**创建一个带有名称和描述的*模拟课程*** 。
*   我们**运行 *addCourse*** 函数，我们期望当请求被执行(当可观察解析)时，我们将得到的响应中的属性名称是'*ches able*'(与 *mockCourse* 中的相同)。
*   然后我们有一个 req URL，当我们调用函数 addCourse 时，我们期望我们可以调用定义为参数 **expectOne** 的端点，这是**检查只有一个请求**。
*   我们还检查了**类型的请求是 POST** 。
*   **然后，我们“刷新”或响应模拟数据，我们将模拟数据作为参数**传递，这导致可观察对象解析并评估第 10 行上的期望。
*   最后，我们从第一个图像运行 afterEach 块到**验证**没有**未决的 HTTP 请求**。

现在我们知道了用 Angular 编写 HTTP 请求测试的步骤，让我们在服务中实现 addCourse 函数:

*   **我们将 HttpClient** (执行 HTTP 请求的 Angular 模块)的私有变量注入到我们的构造函数中。不像在测试中，我们不真正执行真正的 HTTP 请求，感谢 Angular 提供的模拟，我们现在需要使用这个模块。
*   现在**我们创建一个函数( *addCourse* )** ，它将返回一个可观察对象，我们可以从我们的组件订阅这个可观察对象**，以使用它们将携带的数据。**
*   **我们将课程信息作为参数**传递，以便能够保存它。
*   **我们使用指定为 POST 请求数据的过程的 topicId** 来访问端点。

需要注意的一点是，我们将能够直接从响应对象**读取数据，而无需显式解析 JSON** ，这与 Angular 4.3 发布之前的 *HttpModule* 不同。

如果您因为新模块而遇到导入错误，首先确保您当前的 Angular 版本不低于 4.3。

现在，让我们添加测试，按主题获取我们的课程:

在这种情况下，我们测试对象中的所有信息。如你所见，它看起来非常相似。

最后，我们将 *getCoursesByTopic* 函数实现到服务中，与我们使用 *addCourse* 函数的方式相同:

在这种情况下，我们只需要获取一个 topicId 来匹配端点，并只检索特定 topicId 的课程。

**我希望你喜欢这篇文章，并希望你能为自己的服务编写一些测试。**