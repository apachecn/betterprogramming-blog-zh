# 今年你需要改掉的 5 个编码习惯

> 原文：<https://betterprogramming.pub/5-coding-habits-you-need-to-put-to-rest-this-year-432643d97d89>

## 开发人员，让我们避免这些技术

![](img/faea3df122c2fd94e490e2bdbdcf1dc1.png)

由[丹尼尔·赫伦](https://unsplash.com/@herrond?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/no?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在某些圈子里，人们小心翼翼地谈论着一些编码技术。作为一种黑魔法，它们时隐时现，出现的时间足以让你付出沉重的代价。在早期阶段，他们会翻个白眼，叹口气，甚至说脏话。

在他们的后期阶段，愤怒被认为是对这些编码实践的礼貌反应。它们是理智、道德和一般礼仪的诅咒。我认为，没有人能够同时犯下这些大罪，并且灵魂完好无损。

# 评论中的战争与和平

```
// Create a string
String s = "hello";// Add that string to another string
String s2 = s + " world";// Print out the result
System.out.println(s2);
```

在工程学的历史上，从来没有如此少的工程师的行为让如此多的其他工程师血压飙升。这些评论到底是做什么的？它们是干什么用的？我只能得出结论，他们是来让我心烦的，从这个角度来看，他们是一个巨大的成功。在所有其他情况下，它们都是浪费时间。

# 像陀思妥耶夫斯基小说中人物的名字

我想起了世界上最糟糕的计算机科学笑话:“计算机科学中只有两个难题:缓存失效和命名——以及一个接一个的错误。”

```
String myStringWithWhichIWillInputIntoTheCode = "a";String myOtherStringWithWhichIWillAddToMyFirstString = "b";String theUnionOfTheFirstTwoStrings = myStringWithWhichIWillInputIntoTheCode + myOtherStringWithWhichIWillAddToMyFirstString;
```

我在 Java 中经常看到这种情况。当面对一个班级时，除了疲惫的叹息，我从来没见过别的。冗长的名称并不总是不好的，但是您很可能不需要在变量名中使用冠词和介词。

```
String inputString = "a";
String suffix = "b";String fullString = inputString + suffix;
```

# 偶然用拉丁文写的函数

我知道拥有一个名为`applyChangesAndCount`的函数并让它执行不同的、相互交织的进程的复杂舞蹈似乎很聪明。现在，一切都变得如此清晰！现在没人在乎了！！抽象和混淆是有区别的。你显然没有意识到这一点。

```
public long applyChangesAndCount(final List<String> inputs) {
  String output = inputs.stream().distinct().map(s -> s.substring(1)).collect(Collectors.*joining*(","));

  return Arrays.*stream*(output.split(" ")).map(String::toUpperCase).filter(s -> s.equals("HELLO!")).count();
}
```

对，拍拍你自己的背，爱因斯坦。你创造了如此美味复杂的东西，只有你才能理解它与生俱来的天赋。

然而，我敢肯定，对于像你这样的人来说，这真的是一个小问题，如果你能理解代码，你准备好独自保管它了吗？从现在开始，这个代码属于你。你要喂它，带它散步，给它浇水，给它唱歌…我可能被别的东西搞糊涂了。

我的观点是，你不想成为代码片段的唯一主人。责任太大了。你想在你的代码库中自由驰骋。

```
public long applyChangesAndCount(final List<String> inputs) {
  String output = findDistinctWithoutPrefix(inputs.stream())
      .collect(Collectors.*joining*(","));

  return findTokensMatchingValue(Arrays.*stream*(output.split(" ")), "HELLO").count();
}

private Stream<String> findDistinctWithoutPrefix(Stream<String> inputs) {
  return inputs.distinct().map(s -> s.substring(1));
}

private Stream<String> findTokensMatchingValue(Stream<String> inputs, String value) {
  return inputs.map(String::toUpperCase).filter(s -> s.equals("HELLO!"));
}
```

还有很长的路要走，但是现在每一部分都比前一部分稍微好一些了。现在，我们可以深入这些功能，并决定做什么。或许进一步拆散他们？但是这里的关键是代码应该做什么是显而易见的。它现在有了一些语义背景。

记住，复杂并不聪明。简单就是。

# 侵犯我公民自由的测试

听着，如果你想在你的个人生活中进行单元测试，那就给你更多的权力。我吗？我喜欢保持一定的距离，所以当我看到类似这样的单元测试时，我开始担心:

```
@Test
public void TestThis() {
    myMock = // It's a mock, use your imagination.
    myOtherMock = // Yep, another one.

    myTestClass = new TestClass(myMock, myOtherMock);

    String response = myTestClass.myTestFunc();

    assert(response.equals("some expected output"));
    // Okay... so far so good.

    assert(myMock).wasCalledTimes(4).withArgs("something", "somethingelse");
    // Wait what are you doing...

    assert(myOtherMock).wasCalledTimes(3).withArgs("something", "somethingelse");
    // What is wrong with you?
}
```

有一种东西叫做隐私。这在政治上很有用，因为它阻止了你的笨手笨脚的首相成为你的下一个伟大的领导人，，但它在代码中真的很有用。你看，隐私把一切都分开了。您的测试不应该关心它调用了多少次模拟或者它用什么来调用模拟。把你的职能想象成你可怕的反乌托邦编码城市的公民。他们也有权利！

## 但是我怎么知道它调用模拟是正确的呢？

你知道因为输出是正确的。你在这里所做的只是沉溺于一些古怪的，偷窥的幻想。你就像编码界的偷窥狂。如果你是美国人，不知道什么是偷窥狂，就不要用谷歌搜索。这是你搜索历史上的一个污点。这不是什么好事，相信我的话。

# If 语句的金字塔

```
if (something) {
    if (somethingElse) {
        System.out.println("Hello world!");
    } else (somethingFurther) {
        System.out.println("Yes I am still printing something, funny that.");
    }
} else {
    if (somethingElse || somethingFurther) {
        System.out.println("Wow still printing");
    } else if (somethingElse) {
        System.out.println("Still printing.........");

        if (somethingNew) {
            System.out.println("Yes indeed, I am still printing...");
        }
    }
}
```

吉萨金字塔是一个奇妙的景观。这并不意味着你应该在你的代码中模仿它们。你需要一个酸标签，活点地图，和一个显微镜来找到这个烂摊子的底部。

## 但是有时候事情就是这么复杂

是的，是的，好吧。我承认有些情况下大量的 if 语句是不可避免的。在这些情况下，您应该努力:

*   将任何实际的处理都提取到非常清晰命名的函数中
*   尽可能简化你的 if 语句
*   考虑策略模式等替代方案，尽可能地分解功能
*   找份新工作，因为我担心你

# 额外收获:代码中有趣的笑话

我不需要深入这个。你不是一个未被发现的喜剧演员。你是个暴露的虐待狂。专注于告诉人们你的代码实际上做了什么，而不是尝试你失败的单口相声材料。

如果你喜欢这篇博文，欢迎在[推特](https://twitter.com/chris_cooney)上关注我！