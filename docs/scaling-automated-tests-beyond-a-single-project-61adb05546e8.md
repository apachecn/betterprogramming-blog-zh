# 将您的自动化测试代码扩展到单个项目之外

> 原文：<https://betterprogramming.pub/scaling-automated-tests-beyond-a-single-project-61adb05546e8>

## 如何通过使用具有高可读性 DSL 和可重用组件的测试框架来增加自动化测试的 ROI

![](img/fc1a2812102612131ed1e5b0e75aba5c.png)

卡伦·艾姆斯利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片。

本文向拥有不止一个应用程序的公司展示了如何通过具有高度可读的 DSL 和可重用组件的测试框架来增加他们的自动化测试的 ROI。只有一个应用程序的公司仍然可以从使用这里建议的测试框架中获益，原因有很多，我们将会探讨。

*注意:这里的自动化测试主要是指 web 和移动应用的 UI 自动化测试。然而，我们也将看一些 API 测试的例子。*

# 问题是

一般来说，对自动化测试有两种反应。第一反应是这很困难，需要太多的时间，开发人员生产特性的速度比测试人员自动化测试的速度要快，而且我们没有技术或资源来做这件事，所以现在让我们继续手工测试。第二个反应是很容易，就不要在这上面花太多资源了。我们总能在下一次冲刺中赶上——而他们从来没有。

好吧，这里有一个严酷的事实:编写和维护自动化测试并不容易，但是你可以通过使用正确的方法使它变得更容易和更快。

自动化测试不像许多人认为的那么容易的第一个原因是，与十年前相比，现在的 UI 丰富多了。不仅视觉效果越来越吸引人，而且交互——尤其是在移动设备上——也越来越复杂。突然间，我们不再只需要一次滑动，而是同时多次滑动来浏览亚马逊 Prime Video 移动应用程序上的电影列表。更不用说，为了追求更丰富的生活方式，一系列可穿戴物联网设备应运而生。

自动化测试因耗费大量时间和精力而臭名昭著的第二个原因是，敏捷的前景意味着测试经常需要在短时间内做出改变。这些年来，我已经看到了很多与此抗争的自动化代码。快节奏的发布周期需要更智能的测试方法。这不仅仅是关于编写测试，也是关于准备好随时反映需求变化的易于更改的代码，并且确信代码更改不会破坏任何测试。

# 解决方案

为了让测试赶上开发，QA 团队应该向开发团队学习。通常有一个种子项目，开发人员可以用它来立即构建一个应用程序。例如，seed 项目可能包含一个 Angular 项目的样本，以及对来自中央存储库的大量 Angular 组件的访问，您的公司可能已经引入了中央存储库，以保持公司开发的所有应用程序的外观和感觉一致。通过示例学习，还应该有一个自动化测试的种子项目——一个测试框架，它导入一个由与 UI 组件交互的组件组成的库。

其中一个测试组件是在图表中突出显示某个时间段，这当然不是一两个小时就能完成的事情，也不能保证没有错误。考虑到这并不容易，当两个项目使用相同的图形组件时，我们当然不希望在另一个项目中重复这项工作——因此本文的目标是:将测试人员从自动化应用程序中解放出来，让他们专注于真正的测试工作。

使用种子测试项目的另一个原因是，在公司中拥有许多具有不同实现的测试自动化框架实际上没有什么价值，不像微服务的概念那样，给开发团队提供了选择最适合其工作类型的技术和语言的灵活性。事实上，让每个团队灵活地使用他们自己选择的测试框架意味着时间的浪费，因为有些人可能会选择从头开始编写框架，因为每个人都有自己对 Page Object 的解释，所以会混淆测试框架，而且也无法共享测试代码。

这篇文章建议公司只保留一到两个测试框架，这些框架采用剧本模式方法，团队合作产生可重用的测试组件。

# 电影剧本模式

使用剧本模式来扩展有三个令人信服的理由:

1.  正如我们将在下一节中看到的，它使我们能够编写一个高度可读的 DSL。一旦编写了 DSL，任何人(开发人员、手动测试人员、任何团队的 ba)都可以通过适度的学习曲线来使用它来形成测试，这要归功于易于理解和清晰表达的测试方式。这反过来加快了测试编写过程。
2.  它基于参与者、任务、动作和问题的概念。测试是从参与者(应用程序的用户)的角度来描述任务和问题的，而不是从页面或组件的角度。每个任务都是由动作(甚至是任务)组成的。例如，搜索航班是旅游网站上的一项任务，输入目的地是该任务的操作之一。我们想要回答的问题是返回的匹配航班总数。以这种方式编写它们意味着操作(“在输入字段中输入值”)和问题(“列表中返回的项目总数”)可以在共享库中重用，因为它们是大多数应用程序中常见的基于解决方案的交互。甚至基于业务的交互也可以跨项目共享。例如，您公司另一个部门的某人可能已经编写了通过 API 调用从 Google Calendar 添加、删除和检索事件的测试代码。如果你的应用程序依赖于相同的交互，那么你就可以重用共享任务，而不用看一眼 Google API 文档。或者你团队的应用程序依赖于另一个团队的应用程序创建的客户数据。同样，您可以在运行您的测试之前，重用其他团队共享的剧本任务来创建测试数据。
3.  在它的核心，剧本模式是坚实的设计原则的应用。这些原则所带来的令人满意的特性，如关注点分离、高内聚和松散耦合，为一个干净、可维护和可扩展的测试代码铺平了道路。

# 为什么不是页面对象？

一个经验丰富的测试人员会发现 Page Object 缺乏一个可伸缩和可维护的测试套件。许多人试图通过使用页面对象的变体来克服这些弱点。在我看来，到目前为止最好的变化是将 UI 分成组件而不是页面，并在 UI 和测试之间引入一个抽象层。 [John Smart 的文章](https://johnfergusonsmart.com/page-objects-that-suck-less-tips-for-writing-more-maintainable-page-objects/)讲述了如何编写“更简单”的页面对象，进一步建议如何通过在页面对象中只存储定位符、保持页面对象不受业务逻辑的影响，以及将抽象层分为动作和问题来改进这种方法。接下来，应用坚实的原则，用物体而不是方法来表现动作和问题，我们自然会得出剧本模式。

请到下面的参考资料部分阅读解释您为什么想要使用这些技术的文章。读完文章后，您应该会对提到的技术的好处深信不疑。

也许你已经在使用 Page Object 了，并打算只使用上面的一些技术，而不完全依赖于使用 Screenplay 模式。我将向您展示为什么您仍然想要考虑重写您的整个测试框架或者切换到支持剧本模式的测试框架。

我们在前面的章节中已经确定了一个流畅的 DSL 对于满足我们的可伸缩测试框架的目标是非常有吸引力的。让我们以 [Cheapflights](https://www.cheapflights.co.uk/) 或者任何一个旅游网站为例。假设用户想要在 2020 年 10 月 1 日从伦敦飞往巴厘岛，并在 2020 年 10 月 31 日返回。在页面对象世界中，动作“搜索航班”将由随后的方法表示。这是包含与网站交互的实现的方法，您可以从步骤库中调用该方法。我们不打算深入探讨为什么不应该在 step 库本身中实现。

```
searchFlights("London", "Bali", "1 October 2020", "31 October 2020");
```

为了更好地理解这一点，您可以使用生成器模式，这样调用方法就像这样:

```
Flight flight = Flight.builder()
                      .from("London")
                      .to("Bali")
                      .departOn("1 October 2020")
                      .returnOn("31 October 2020")
                      .build();searchFlights(flight);
```

现在看起来更好，但剧本模式可以使它更加优雅。这是可能的，因为动作“搜索航班”是一个对象:

```
SearchFlights.from("London")
             .to("Bali")
             .departOn("1 October 2020")
             .returnOn("31 October 2020");
```

我们再举一个网站上的例子。完成基本搜索后，用户会看到搜索结果和过滤结果的选项。一些选项以滑块的形式出现。您可以为 slider 组件创建一个 Page 对象，它接受 slider 容器的 data 属性作为参数，以区分结果页面上的滑块。使用中途停留持续时间滑块的构建器模式，代码如下所示:

```
Slider stopoverDurationSlider = Slider.builder()
                      .id(stopoverDurationSliderId)
                      .min(30)
                      .max(120)
                      .build();
slide(stopoverDurationSlider);
```

如果你使用电影剧本模式，把它和下面的比较一下。同样，请注意后者看起来更加流畅和自然:

```
Slide.toMin(30)
     .andToMax(120)
     .the(stopoverDurationSlider)
```

这就是了。不使用页面对象的充分理由。

# 使用哪个测试框架

如果你喜欢使用剧本模式，但是不知道如何或者从哪里开始，不要担心，因为已经有一些开源测试框架支持它。

[Serenity BDD](https://johnfergusonsmart.com/serenity-bdd/) 是一个基于 Java 的测试框架，支持页面对象和剧本模式。它在用于 web 自动化的 Selenium Webdriver 和用于 BDD 的 Cucumber/jBehave 之上充当包装器。您还可以将它与 Appium 一起用于移动和桌面自动化——参见 [Appium for Mac](https://github.com/appium/appium-for-mac) 和[app ium Windows Driver](http://appium.io/docs/en/drivers/windows/)——并放心进行 RESTful API 测试。这几乎涵盖了除 Windows 7 之外的所有内容，因为 Appium Windows 驱动程序仅支持 Windows 10 及更高版本。

对于那些喜欢这种语言的人来说，它也有打字稿的味道，即 [SerenityJS](https://serenity-js.org/) 。如果你更喜欢[木偶师](https://developers.google.com/web/tools/puppeteer)或其继任者[剧作家](https://github.com/microsoft/playwright)，SerenityJS 目前不支持它们，但总有机会扩展框架以包含这些库。

Serenity BDD 和 SerenityJS 已经解决了部分问题，它们给了我们很多常见的操作，比如悬停、双击、滚动到目标、移动鼠标到目标，以及更多的操作。另外，请参见可用的[问题](https://github.com/serenity-bdd/serenity-core/tree/4d9cd4d54df0d3e934216c04b4508fcaf5cbea75/serenity-screenplay-webdriver/src/main/java/net/serenitybdd/screenplay/questions)和[匹配器](https://github.com/serenity-bdd/serenity-core/tree/4d9cd4d54df0d3e934216c04b4508fcaf5cbea75/serenity-screenplay-webdriver/src/main/java/net/serenitybdd/screenplay/matchers)。我个人使用 Serenity 已经很多年了，内置的功能给我留下了深刻的印象，包括一个很棒的报告工具。它还集成了 ngWebDriver 来处理异步应用程序。

对于缺失的 Windows 7 覆盖率(毕竟，根据 Perfecto Mobile 最新的 [Windows OS 系列覆盖率报告](https://info.perfectomobile.com/mobile-web-test-coverage-index-reg.html)，与 Windows 10 的 55.72%相比，它仍然拥有 33.44%的市场份额)，我建议使用与 [FlaUI](https://github.com/FlaUI/FlaUI) 集成的 [Specflow](https://specflow.org/) 。Specflow 是一个伟大的。NET 测试框架，支持 BDD 和一些额外的扩展，尽管后者只能通过 Specflow+中的许可获得。与此同时，弗莱伊是一名司机。NET，它使用微软的 UI 自动化库与 Win32、WinForms 和 WPF 桌面应用程序进行交互。好消息是有一个名为 [CSF 的项目围绕着 Specflow，支持剧本模式的使用。剧本](https://github.com/csf-dev/CSF.Screenplay)，灵感来自宁静。因此，如果你一直在宁静中工作，你会很快熟悉它。坏消息是，在撰写本文时，它只有用于 Selenium WebDriver 的[扩展](https://github.com/csf-dev/CSF.Screenplay.Selenium)，因此您必须创建 FlaUI 扩展或等待有人参与项目。

# 概括起来

团队应该开始协作，而不是像自动化测试那样各自为政。拥有一个单一的测试方法是至关重要的，我们在这篇文章中已经看到了为什么剧本模式是一个成功的自动化协作的基石。

简而言之，使用 Serenity BDD Java 测试框架。如果您有额外的能力让测试人员使用 TypeScript，也可以提供 SerenityJS。最后，通过 CSF 添加对 FlaUI 的支持。剧本，如果你关心你的 Windows 7 用户群。

# 资源

[](https://johnfergusonsmart.com/page-objects-that-suck-less-tips-for-writing-more-maintainable-page-objects/) [## 少吸的页面对象——编写更易维护的页面对象的技巧——约翰·弗格森·斯马特

### 页面对象是最广泛使用的测试自动化设计模式之一。大多数 QA 工程师都使用过…

johnfergusonsmart.com](https://johnfergusonsmart.com/page-objects-that-suck-less-tips-for-writing-more-maintainable-page-objects/) [](https://dzone.com/articles/page-objects-refactored-solid-steps-to-the-screenp) [## 页面对象重构:迈向剧本/旅程模式的坚实步骤

### 七年多来，PageObjects 一直是自动化 web 测试的主要工具。西蒙·斯图尔特写了原著…

dzone.com](https://dzone.com/articles/page-objects-refactored-solid-steps-to-the-screenp) [](https://www.infoq.com/articles/Beyond-Page-Objects-Test-Automation-Serenity-Screenplay/) [## 超越页面对象:使用 Serenity 和剧本模式的下一代测试自动化

### 自动化验收测试减少了手动测试和错误修复中浪费的时间，当与…

www.infoq.com](https://www.infoq.com/articles/Beyond-Page-Objects-Test-Automation-Serenity-Screenplay/)