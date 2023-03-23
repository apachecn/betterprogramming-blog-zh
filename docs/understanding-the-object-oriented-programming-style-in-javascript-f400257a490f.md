# 理解 JavaScript 中面向对象的编程风格

> 原文：<https://betterprogramming.pub/understanding-the-object-oriented-programming-style-in-javascript-f400257a490f>

## 了解今天仍然适用的经典编程范例

![](img/7585d9759318a2adf4d79ddcafc275e0.png)

照片由[乔安娜·科辛斯卡](https://unsplash.com/@joannakosinska?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是 JavaScript 编程风格系列文章的第四篇。我之前已经介绍过 m[on lithic](/understanding-the-monolithic-style-in-javascript-f86f78b21536)，c [ookbook](/whats-the-cookbook-style-in-javascript-be98658d5be1) 和 p [ipeline](/whats-a-pipeline-style-in-javascript-2084b66133f0) 样式。本文介绍了当今最流行的编程范式——面向对象(OO)风格。在本文中，我将展示面向对象的一种常见的子风格——用对象编程。

和我以前的文章一样，我将统计乔纳森·斯威夫特的经典文章《一个适度的提议》中的用词频率。

# 什么是对象？

如果你在高中或大学学过编程课程，或者了解编程文献，那么你已经了解了面向对象编程(OOP)的概念。OOP 引入了程序应该模拟真实世界形成方式的思想，这意味着程序是由与其他对象交互的对象组成的。

例如，我使用 objects 方法编写的跟踪学生考试成绩的程序将有一个`student`对象、`test`对象、`course`对象，甚至可能有一个`grading-scale`对象，这取决于我如何编写程序。

每个对象都包含自己的数据和处理数据的函数(有时称为*方法*)。如果我愿意，对象可以与其他对象对话。如果我想真正在 OO 范式中工作，我将创建从其他对象继承属性(数据和函数)的对象，这样我就不必不断地重新定义保持不变的属性。例如，如果我正在编写一个关于汽车的程序，我不需要为每种类型的汽车定义方向盘，除非特定类型汽车的方向盘看起来或行为不像大多数汽车方向盘。

有一些重要的概念组成了 OO 范式。*传承*就是其中一个重要的概念。另一个是通过封装隐藏数据，这是面向对象程序的一个重要特征。*隐藏数据*意味着不允许自由访问对象的数据(没有这个，对象的用户可以将任何有效的数据分配给对象的一个属性)。

一个例子是具有年龄属性的`student`对象。年龄通常被定义为整数，大多数编程语言允许整数保存从-20 亿到+20 亿的数字。很明显，一个学生的年龄必须在 0 到 120 岁之间，所以我需要保护他的财产不被随意分配。

我可以通过要求年龄只能通过检查以确保输入的年龄是有效年龄的函数来分配来保护学生的年龄。该功能成为对象界面的一部分，即用户与对象交互的方式。

另一个与继承相关但又不同的重要概念是*组合。*当继承模型是-a 关系时，组合模型具有-a 关系。例如，如果我正在构建一个`student`对象，那么一个学生就有一个名字。一个学生有专业。学生有一份已完成课程的清单。Has-a 关系与 is-a 关系一样重要(如果不是更重要的话),但是在讨论 OO 编程的惊人特性时经常被忽略。

我将要演示的词频程序专门关注 has-a 关系。我要实现的主要对象是词频控制器对象(WFC)。WFC 将有一个单词管理器(WM)、一个停用单词管理器(SWM)和一个词频管理器(WFM)作为其组成部分。这些部分没有可以沿着继承链继承的特征，因为每个经理都完全独立于其他经理。

除了这个特殊的问题(词频)不适用于继承解决方案之外，软件工程师 Joshua Bloch 在他的书《有效的 Java》中概述了继承的几个问题在本书中，Bloch 认为，因为继承会导致封装失败，所以对于大多数应用程序来说，使用组合通常比继承更好。我强烈建议你拿起这本书，即使你不打算做任何 Java 开发。他的见解在很大程度上适用于大多数编程语言。

# 面向对象编程的真正简史

第一个面向对象的编程语言是 [Simula](https://en.wikipedia.org/wiki/Simula) ，由奥利·约翰·达尔和克利斯登·奈加特于 20 世纪 60 年代在挪威计算中心首次开发。Simula 是作为 ALGOL 60 语言(最有影响力，但未得到充分利用的编程语言之一)的超集开发的，它引入了类、继承、虚拟过程和协程的概念。

大多数(如果不是全部的话)现代 OO 编程语言，包括 C++、Java 和 C#，都受到了 Simula 的极大影响，这些语言的许多主要开发者也是如此，如比雅尼·斯特劳斯特鲁普(C++)和詹姆斯·高斯林(Java)。

继 Simula 之后，下一个认真采用 OOP 的语言是 [Smalltalk](https://en.wikipedia.org/wiki/Smalltalk) 。Smalltalk 是在 20 世纪 70 年代中后期由计算机科学家 Alan Kay 作为主要语言设计者在 Palo Alto 研究中心开发的。

Smalltalk 最初是作为一种教育应用程序语言开发的，Kay 从教育语言徽标中获得了很多灵感。尽管如此，Smalltalk 仍然在许多科学和商业应用程序中找到了自己的路，许多未来的 OOP 语言使用了 Smalltalk 中首次开发的特性。

在 Smalltalk 之后，开发了诸如 C++、Java 和 C#之类的语言，将 OOP 的特性与更传统的命令式特性相结合。当然，Java 在它的 OOP 理念上走得最远，语言的每个方面都是面向对象的，以至于甚至写一个“Hello，world！”程序需要创建一个对象来显示消息。

# 一个面向对象的词频统计程序

这就把我们带到了当前的任务——设计一个展示传统的面向对象特性的词频计数程序。第一项任务是将问题分解成对象。这个问题由三个主要对象组成:1)我们从中提取单词的文本；2)我们想要从文本中移除的停用词；以及 3)词频本身。

这个结构提出了三个类:一个存储文本的`WordsManager`类，一个存储停用词的`StopWordsManager`类，以及一个对词进行计数并存储最终的词频排序列表的`WordFreqManager`类。

我需要在这个列表中添加第四个控制程序的类。这个类叫做`WordFreqController`，熟悉[模型-视图-控制器设计模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) (MVC)的人会明白我为什么使用控制器类。我的程序的结构更像是模型-控制器结构，但是 MVC 模式可以作为理解程序结构的基础。

事不宜迟，下面是我的词频统计程序，以面向对象的方式编写:

```
class WordsManager {
  constructor(text) {
    this.wordset = read(text);
  } parse_words() {
    let wordList = [];
    let index = 0;
    let word = "";
    while (index < this.wordset.length) {
      let c = this.wordset[index];
      c = c.toLowerCase();
      if (c >= "a" && c <= "z") {
        word += c;
        index++;
        continue;
      }
      index++;
      if (word != "") {
        wordList.push(word);
      }
      word = "";
    }
    this.wordset = wordList;
  } get words() {
    this.parse_words();
    return this.wordset;
  }
}class StopWordsManager {
  constructor(file) {
    this.stopWords = read(file);
    this.stopWords = this.stopWords.replace(/(\r\n|\n|\r)/gm, "");
    this.stopWords = this.stopWords.split(",");
  }

  get text() {
    return this.stopWords;
  } isStopWord(word) {
    if (this.stopWords.find(aWord => aWord == word)) {
      return true;
    }
    return false;
  }
}class WordFreqManager {
  constructor() {
    this.word_freqs = new Map();
  } increment(word) {
    if (this.word_freqs.has(word)) {
      this.word_freqs.set(word, this.word_freqs.get(word)+1);
    }
    else {
      this.word_freqs.set(word, 1);
    }
  } sort() {
    let wordFreqList = Array.from(this.word_freqs);
    for (let i = 0; i < wordFreqList.length-1; i++) {
      for (let j = 0; j < wordFreqList.length-i-1; j++) {
        if (wordFreqList[j][1] < wordFreqList[j+1][1]) {
          let temp = wordFreqList[j];
          wordFreqList[j] = wordFreqList[j+1];
          wordFreqList[j+1] = temp;
        }
       }
    }
    return wordFreqList;
  }
}class WordFreqController {
  constructor(file) {
    this.words_manager = new WordsManager(file);
    this.stopwords_manager = new StopWordsManager("stopwords.txt");
    this.wordFreq_manager = new WordFreqManager();
    this.word_count = [];
  }

  run() {
    for (let word of this.words_manager.words) {
      if (!this.stopwords_manager.text.includes(word)) {
        this.wordFreq_manager.increment(word);
      }
    }
    this.word_count = this.wordFreq_manager.sort();
    const num_words = 15;
    for (let i = 0; i < num_words; i++) {
      print(this.word_count[i]);
    }
  }
}// main program
let wfc = new WordFreqController("text.txt");
wfc.run();
```

下面是这个程序的输出:

```
children,18
kingdom,15
thousand,15
country,11
own,10
child,10
hundred,8
parents,7
little,7
shillings,7
nor,7
publick,6
food,6
pounds,6
time,5
```

# 还有其他面向对象的风格

我在这里展示的程序仅仅展示了一种 OOP 风格。这种风格是大多数学生在学校学到的，所以它通常被认为是编写面向对象程序的唯一方法。但实际上，面向对象的风格要多得多。在我的下一篇文章中，我将展示另一种面向对象的风格，Christina Lopes 称之为*信箱*风格。

感谢阅读。