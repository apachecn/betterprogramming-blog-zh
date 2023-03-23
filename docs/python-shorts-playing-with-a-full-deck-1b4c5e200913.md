# 蟒蛇皮短裤——搭配全套服装

> 原文：<https://betterprogramming.pub/python-shorts-playing-with-a-full-deck-1b4c5e200913>

## *一系列简短的 Python 编程示例。今天我们将从一副洗牌的牌中发牌，没有重复，也没有任何数量的玩笑，使用两个你可以在自己的纸牌游戏创作中使用的简短而甜蜜的函数。*

![](img/9d57e5a8d656530d5a4d2d5041e4ac57.png)

照片由 Praveen kumar Mathivanan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Python 非常适合创建各种各样的游戏，包括那些涉及一副纸牌的游戏。下面的短程序演示了两个功能；一个建立并洗一副牌，包括任意数量的小丑，一个从这样一副牌中发牌，没有重复。这个简单的代码可以很容易地提供一个复杂得多的游戏设计的核心。

# 创建甲板

正如您可能猜到的，对于创建和洗牌的程序来说，导入 random 模块是必须的。使用`range()`创建一副牌，作为一个简单的 0 到 51 的整数列表(加上选定数量的小丑)。列表被打乱并返回，准备分发。这是目前为止的代码。

```
from random import *def get_deck(jokers):
    deck_size = 52 + jokers
    deck = list(range(deck_size))
    shuffle(deck)
    return deck
```

## 发牌

对于要分发的每张牌，该副牌被传递给`get_card()`，这是一个从该副牌中抽取一张牌的功能。每张卡片都被表示为一个小整数，因此该函数处理该整数以形成卡片的字符串描述符。例如“黑桃 a”、“方块 7”或简单的“小丑”。

![](img/f8c6e8f09bcf953bf6299926bee6d6df.png)

由[克利姆·穆萨利莫夫](https://unsplash.com/@klim11?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 玩一副空牌

请注意，`pop()`方法用于从洗牌的一副牌中抓取一张牌。这样每调用一次`get_card()`，列表的长度就缩短一，如果卡组的长度缩短为零，那么返回的卡串将被返回为“卡组为空”。在大多数纸牌游戏中，这将是再次跟注`get_deck()`的强烈信号！

这是发牌的代码，一次发一张，不要重复。

```
def get_card(deck):
    if len(deck) == 0:
        return "Deck is Empty"
    card = deck.pop()
    if card > 51:
        return "Joker"
    suit = ["Hearts", "Clubs", "Spades", "Diamonds"]
    face = [
        "Ace",
        "2",
        "3",
        "4",
        "5",
        "6",
        "7",
        "8",
        "9",
        "10",
        "Jack",
        "Queen",
        "King",
    ]
    return face[card % 13] + " of " + suit[card // 13]
```

# 这是件大事

为了演示这两个函数，下面的代码创建了一副有两个小丑的牌，然后发了七张牌。随意编辑笑话和卡片的数量，看看会发生什么。例如，如果您将开玩笑者的数量设置为 0，并分发 55 张牌，则输出的最后三行应该都显示为“甲板是空的”。

```
jokers = 2
cards = 7deck = get_deck(jokers)
for i in range(cards):
    print(get_card(deck))# 8 of Spades
# Jack of Spades
# 4 of Spades
# 2 of Hearts
# King of Diamonds
# 7 of Spades
# 10 of Spades
```

当然，我总是建议你玩全套牌。那样生活会更好！

```
**Want to Connect?** John is the author of [Python for Numworks](https://amzn.to/3k4O39j) , [Python for OpenSCAD](https://amzn.to/3mHK0Br), [Python for the TI-Nspire CX II](https://read.amazon.com/kp/embed?asin=B09DJ7MRDX&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20), [Python for the TI-84 Plus CE Python calculator](https://read.amazon.com/kp/embed?asin=B09GJMBDMF&preview=newtab&linkCode=kpe&ref_=cm_sw_r_kb_dp_Z6AHQNW2MQBZHCNKC38G&tag=solarproud-20), [Python for CASIO Calculators](https://amzn.to/3mgX0js), and [many other titles](http://johnclarkcraig.com/).
```