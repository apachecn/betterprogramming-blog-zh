# 软件组件的名字应该古怪而神秘

> 原文：<https://betterprogramming.pub/software-component-names-should-be-whimsical-and-cryptic-ca260b013de0>

## *被认为有害的描述性名称。*

![](img/bc70bee69c525e3d0ac72eee469f5a1b.png)

准确的标签很难(作者供图)。

我经常看到这样的行动号召:“工程师们，请更描述性地命名你们的服务/回购/库！

我知道用《指环王》中的大蜘蛛来命名你的网络爬虫很有趣，但是当我们不得不记住 Shelob 是什么的时候，这对局外人来说太难了。

就给它起个名字`InternalWebCrawler`然后在自述文件里放一张蜘蛛的有趣图片什么的。"

不。从长远来看，不。这是个坏主意。

看，事物的范围和目的比它的名字变化得更快。为了好玩，我刚才在 Github READMEs 上搜索了“尽管有名字”这个短语。大概两万个结果。以下是第一页的一些内容:

*   `[django-htmlmin](https://github.com/cobrateam/django-htmlmin)`:用于 Python 框架的 HTML minifier(不仅仅是 Django，尽管有这个名字)。
*   `[plugin-update-checker](https://github.com/YahnisElsts/plugin-update-checker)`:尽管有这个名字，它也和主题一起工作。
*   `[svox2](https://github.com/sxyu/svox2/blob/master/README.md)`:尽管有这个名字，但严格来说它并不是 svox 的继任者。
*   `[CO_Cron](https://github.com/ColoradoDemography/CO_Cron)`:不管名字如何，使用节点调度而不是 cron。
*   `[TensorFlow Infra Validator](https://github.com/tensorflow/tfx/blob/master/docs/guide/infra_validator.md)`:顾名思义，不验证用于运行 TensorFlow 的基础设施。

最后一个故事特别具有警示意义。TensorFlow 被开发机器学习系统的人大量使用，从 README 中，人们可以感觉到，至少有一些人被这个名字欺骗了，产生了一种错误的安全感。希望那些人没有在你车里研究人工智能。

我可能有点夸张了，但我希望我的观点是清楚的。“描述性”的名字并不能创造透明性，它们创造了透明性的幻觉。如果你看到一个名为 OrderStatusService 的东西，你会本能地认为你知道它是什么，做什么，你很可能错了。

(更糟糕的是那些每个人都用来交流软件的无处不在的图表，其中有一个标记为 OrdersService 的框，一个箭头将它连接到一个标记为 OrderStatusService 的框。我不明白为什么有人会画这些。您只需*说*“*订单服务异步地将订单 id、eta 和状态推送到订单状态服务。”如果你画一张图，任何想相信数据在另一个方向流动，或者是同步的，或者是不同的数据的人，都会看着图并看到确认。)*

*现在，如果一个名字将很容易永远改变，请让它具有描述性。我更愿意维护变量看起来像`numCols`和`numRows`而不是`i`和`j`的代码。(只是，看在上帝的分上，如果换个意思，也换个名字)。但是如果一个名字在任何意义上都是一个标识符，一个可以从很远的地方指向一个复杂事物的东西，那就让它成为一个不透明的标识符。在数据库模式设计中，您会得到类似的建议——如果您的用户的电子邮件地址可以更改，不要将他们的电子邮件地址用作数据库中的外键。请改用数字或随机字符串。一些不变的东西。*

*在人类的世界里，数字和随机字符串不是友好的标识符。相反，这里是我对名字的建议准则。*

*   *名字要好记，好拼写。*
*   *名称不应该与您正在处理的领域有太多关联。将你的动物园管理软件的一部分命名为“老虎”可能感觉很可爱，但最终有人会在你的日志文件中搜索“老虎”，并看到许多与实际老虎无关的东西。*
*   *名称通常不应包含您公司的名称。如果 BongCo 的所有东西都以 Bong 开头，那么它只是让名字更长，而没有添加信息。*
*   *名字应该让你微笑。是的，特别是你。每当你创造的东西出现时，你都应该得到多巴胺的帮助，即使是在像“Shelob 又坏掉了”这样的句子中快乐是最重要的事情之一。*
*   *名字不应该描述你现在认为你命名的东西是为了什么。想象给你的新生儿取名为医生，或`SupportsMeInMyOldAge`。可怜的孩子。*

*![](img/0b3716682496e610edc581c163f1ee4b.png)*

*Shelob 又崩溃了(Dall-E 原创艺术)*