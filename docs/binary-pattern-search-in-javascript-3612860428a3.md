# 面试问题:JavaScript 中的二元模式搜索

> 原文：<https://betterprogramming.pub/binary-pattern-search-in-javascript-3612860428a3>

## 比较小写字母 a-z 的字符串和 1 和 0 的模式

![](img/f58b96ec6e39eae09888bfc77ca12d72.png)

塔赫勒·阿明在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正如你们中的一些人所知，我正在市场上寻找我在 web 开发方面的下一个机会。这意味着我每天都在办公桌前做两件事之一:

1.  浏览空缺职位，申请所述职位，并在 LinkedIn 上与人联系。
2.  学习新的概念、框架和技术，因为这很有趣。*

*也是为了在我的简历中添加更多技能，并作为一名开发人员不断成长。

我想学的东西每天都在增长，但我决定优先考虑 JavaScript 框架、算法和 AWS 认证。我在[熨斗学校](https://flatironschool.com/)的时间给了我很多实践经验，我喜欢学习足够多的知识来建造一些东西，然后投入其中去建造它。然而，当我毕业时，我很快意识到我还有很长的路要走，还有很多东西要学。

我还没有完成我的 Angular 类，但我已经决定投入并最终致力于我的算法技能(我已经在 HackerRank 和 LeetCode 上做了一些，但没有太多的主题或目的)。

我确实明白，算法实际上只是配方，你可以一步一步地将更小的运算组合在一起，处理复杂的算法通常需要善于将挑战分解为更小的可管理的块，但我知道有些配方远非直观。值得学习一些行之有效的技巧。

这就是我在 Udemy 上与柯尔特·斯蒂尔一起报名参加[JavaScript 算法和数据结构大师班的原因。](https://www.udemy.com/course/js-algorithms-and-data-structures-masterclass/)

对于这个类，我不能说太多好的东西，我可以全心全意地把它推荐给任何代码爱好者，他们和我一样，来自非技术背景，但仍然希望更深入地研究一些可怕的概念，如大 O、递归、排序算法等等。

柯尔特是一个很棒的老师，这门课充满挑战，但也足够有趣，让我每天都想回来上更多课。这门课还帮助我完成了最近的一次编码面试。

最近，一家公司联系了我，希望能更好地了解我这个候选人。我从我最近的经历中学到了一些东西，我在一篇两部分的文章中写道:这里是，，不要太激动，但还是要尽力而为。

我在周末收到了一份要完成的编码评估，并提醒自己每一次代码挑战、评估和面试都让我成为一名更好的开发人员，让我更接近我的理想工作，即使这次没有成功。

我点击了提供给我的链接，开始了挑战，结果是一个由两个算术任务和七个反应问题组成的限时评估。

为了不影响他们的面试过程，我不会透露网站和公司的详细信息。然而，第二个算术任务非常有趣，值得一写。我将改变一些细节，并在这里解释一下描述。

**任务:**编写一个函数`binaryPatternMatching`，它采用一个由 1 和 0 组成的序列和一个由小写字母组成的字符串组成的模式。然后，该函数应该返回所有可能匹配的计数，其中 0 对应于小写元音，1 对应于小写辅音(为了澄清，匹配必须具有与模式相同的长度和顺序)。

比如说。给定一个字符串`010`，这是我们的模式，和另一个字符串`amazing`，我们将比较我们的模式，我们可以看到我们有两个可能的匹配。

```
**010**
**ama**zing **010**
am**azi**ng**matches = 2**
```

让我们试试更长的绳子。在这里，我向[随机句子生成器](https://randomwordgenerator.com/sentence.php)寻求一些帮助，下面是它得出的结果:

> "有三块球形岩石聚集在一个立方体房间里。"

```
 **010**
Th**ere** were three sphered rocks congregating in a cubed room **010**
There w**ere** three sphered rocks congregating in a cubed room **010**
There were three sph**ere**d rocks congregating in a cubed room **010**
There were three sphered rocks congr**ega**ting in a cubed room **010**
There were three sphered rocks congreg**ati**ng in a cubed room **010**
There were three sphered rocks congregating in a c**ube**d room**matches = 6**
```

出于这个任务的考虑，我们假设空格打断了字符串，这意味着它不再是一个匹配，这就是为什么以下内容没有被计算在内:

```
 **0 10**
Ther**e we**re**01 0**
**in a** cubed **0 10**
in **a cu**bed
```

我相信有几种方法可以完成这项任务，我在这里提出我的解决方案。它并不完美，或许还可以改进，但考虑到时间限制和我目前的知识，我粗略地写了以下内容。

我首先查看了我的输入和输出。我的输入是一串 1 和 0 以及另一串小写字母。我在我的算法类中见过类似的问题，其中字符串和子字符串(这里称为*模式*)都是字母或数字。我设计了一个两步走的计划:

1.  将小写字母字符串转换为 1 和 0，其中辅音将变成 1，元音将变成 0。
2.  对两个字符串(string 和 substring)进行循环，并计算模式匹配的次数。

以下是我如何实施第一步的:

```
// convert the string into an array of characters
let subStrings = s.split('')// create a new variable where we will store our 1s and 0s
let arrayInBinary = []// for every element of our chars array
for (let char of subStrings) { // handy regular expression matching function
    // returns true if the char is a vowel
    if (/[aeiouy]/.test(char) === true) { // we then push a "0" into our new array
        arrayInBinary.push(0)

    // we push a "1" into our new array
    } else if (/[bcdfghjklmnpqrstvwxz]/.test(char) === true) {
        arrayInBinary.push(1)
    // if the char is an empty space, we push space into the array
    } else {
        // push an empty space into new array
        arrayInBinary.push(' ')
    }
    // we then convert the array back into a string
    let string = arrayInBinary.join('')}
```

这就是第一步。我们现在可以处理两个 1 和 0 的字符串，这使得这个任务更容易管理。

继续第 2 步:

我们现在需要比较这两个字符串并找到匹配的模式。由于我最近搜索算法的实践，我知道了实现这一点的函数。这是我们函数的第二部分:

```
// a variable to keep track of our matches
let count = 0// I reassigned pattern to a new variable for convenience
let substring = pattern// loop over our string (the long one)
for (let i = 0; i < string.length; i++) { // loop over our substring ("010")
    for (let j = 0; j < substring.length; j++) { // we are comparing the substring[j] to string[i+j]
        // because 'j' changes inside the loop but 'i' does not
        if (substring[j] !== string[i+j]) { // break out of the loop if no nums not a match
            break // if 'j' made it to the end of the short string
        } else if(j === substring.length -1) { // we have a match and we increase out counter
            count++ } }}
```

现在，我确信有一个更快的解决方案(这个方案的时间复杂度为 O(n**2)，但是我时间很紧，如果我手中已经有了一把非常好的锤子，我也没有心情去寻找钉枪(我喜欢木工和 DIY，以防你不知道)。

下面是整个函数——这次没有注释:

同样，由于嵌套循环，我们处理的时间复杂度为 O(n**2)，这不是很好。我在此发誓，当我学完我的算法课程，或者在不远的将来，当我更有经验、更有智慧的时候，我会回来重构代码并更新这篇文章。

编辑:

我收到了一些反馈(有些是建设性的，有些不那么积极),因此我重构了这个函数。

这是一项正在进行的工作，我仍然愿意听取如何摆脱这种嵌套循环的建议。我按照某人的建议尝试使用 String.matchAll()，但不幸的是，它忽略了重叠的字符串(给定(' 010 '，' amazing ')，返回 1)，因此，返回错误的匹配数。我将把它注释掉。

第二版:

经过一些研究和一些思考，我现在有了这个优雅的解决方案，它使用正则表达式，在比较子串和二进制模式时消除了嵌套循环。低看一看:

下次见！