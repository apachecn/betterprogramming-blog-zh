# NextJS 中如何选择取数据策略

> 原文：<https://betterprogramming.pub/how-to-choose-data-fetching-strategies-in-nextjs-dadabf45e562>

## 用真实世界的例子解释

![](img/b7c1b3f831cdedd1e731d1cec160044f.png)

照片由[布伦丹·丘奇](https://unsplash.com/@bdchu614?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/confusion?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

[**NextJS**](https://nextjs.org/) 是我们 React 开发者喜爱和推崇的一个框架。需求量很大，而且非常非常强大。

但是当你开始学习这个的时候，你会看到 **ReactJS** 和 **NextJS** 主要区别在于`pre-rendering`的能力。一开始可能有点难以理解。

有多种策略可供我们选择，一开始在正确的地方应用正确的解决方案会让人感到困惑。我们将采用现实生活中的应用程序，并了解哪种策略最适合哪种场景。

让我们开始吧。

# 这些功能

希望你已经对 NextJS 有了基本的了解。如果没有，我建议你先看这篇[文章](https://javascript.plainenglish.io/start-your-journey-with-next-js-958705cfc299)。

NextJS 中用于预渲染的特殊函数(ReactJS 中没有)有:

*   `getStaticProps`
*   `getStaticPaths`
*   `getServerSideProps`

我们将看到您可以在哪些场景中应用这些功能，并了解最佳实践。

# 我们正在谈论一个电子商务网站

使用 React 的最大缺点是有点难以做到 SEO 友好。但是对于一个电子商务网站来说，这是你需要的主要东西之一。

因此，让我们假设我们正在建立一个有多个页面的电子商务网站。让我们开始设计这个。

# 产品页面(可预测)

我们电子商务网站的主页是产品页面，您可以在这里看到产品列表。显然，你已经知道要展示哪些产品。可以有多种策略，如:

*   获得流行产品
*   获取特色产品
*   获得打折产品

不管是哪种情况，最重要的是你已经知道你需要调用哪个 API，并且这个 API 不会改变或者依赖于其他信息。在这些场景中只有`getStaticProps`就足够了。

现在我们的产品页面会提前预生成，不会改变。

# 产品页面(动态)

但是如果你正在建立一个网站，其中的顶级产品根据某种算法经常变化，那么你就不希望一直显示同一个页面。

在这种情况下，您需要确保生成的页面定期更新。

然后，你必须使用`revalidate`属性。我们可以定义一个以秒为单位的数字来表示刷新间隔。

我们给的值是 10。这意味着我们仍在预生成我们的产品页面，但它将每 10 秒更新一次。这叫`incremental static re-generation`。

# 产品详情页面(可预测但动态)

现在，当我们的用户点击某个产品时，我们必须显示该产品的详细信息页面。我们正在调用一个类似`/product-details/?productId=123`的 API。

有趣的是，我们不知道我们要找的是哪一只`productID`。所以我们的服务器需要提前知道所有有效的`productId`。

这就是`getStaticPaths`的用武之地。它告诉服务器我们需要预先生成的所有可用路线。

现在，只有带有有效`productId`的页面会被预生成。

# 帐户页面(动态和安全)

现在有一些页面，我们想验证一些东西。例如在用户的简档页面上。我们不想预先生成所有用户的个人资料，并在需要时显示它们。

或者，我们可能希望确保用户有查看页面的正确权限。在这些场景中，我们使用`getServerSideProps`。

记住一点:如果你使用`getServerSideProps`，它不会预生成任何页面。相反，它会在每次页面被请求时运行`getServerSideProps`中的代码。

所以这样更安全但效率低。你会想尽量少用它。

还有一个场景你甚至不能使用`getServerSideProps`。

# 实时访问者数量(非常高度动态)

现在，如果你正在设计一个页面，需要显示一些每次都会改变的数据，例如，你的网站的活访问者数量，你不能使用以上四种方法中的任何一种。相反，你不得不依靠传统的数据获取。

为此，您可以使用任何库，如`axios`、`fetch`、`useSWR`等。那是你的选择。

# 结论

就是这样。现在，您应该清楚地了解了现代应用程序的可能场景，以及如何利用 NextJS 中的预渲染功能来提高应用程序的性能和 SEO 支持。

如果你有任何意见，请告诉我。

祝您愉快！

**通过** [**LinkedIn**](https://www.linkedin.com/in/56faisal/) **或我的** [**个人网站**](https://www.mohammadfaisal.dev/) **与我取得联系。**