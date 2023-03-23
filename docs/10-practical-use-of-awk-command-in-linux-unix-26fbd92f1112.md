# AWK 命令在文本处理中的 10 个实际应用

> 原文：<https://betterprogramming.pub/10-practical-use-of-awk-command-in-linux-unix-26fbd92f1112>

## Linux 或 Unix 中的 AWK 实践指南

![](img/875ca2b25281ad0d298eb498781289c7.png)

摄影:unsplash.com 的 Derek Oyen

AWK 是一种模式扫描和文本处理语言。它代表了作者的名字" **A** ho、 **W** einberger 和 **K** ernighan "。

除了作为一个 unix 或 linux 命令，`awk`被认为是一种专门为处理文本而设计的编程语言。

一些系统管理员避免 AWK，因为他们认为你需要了解编程才能获得它的有用性。但是有一些在对编程一无所知的情况下使用 awk 的实用方法。这就是本教程的全部内容。

在我们开始之前，本教程假设您已经熟悉如何操作 Linux 或类 Unix 环境。

所以，让我们开始设置我们的例子。创建一个包含以下内容的文本文件。然后另存为`jobclass.txt`:

```
1 Warrior Melee Str
2 Knight Melee Str
3 Mage Range Int
4 Priest Melee Int
5 Hunter Range Agi
6 Assassin Melee Agi
7 Summoner Range Int
8 Bard Range Int
9 Paladin Melee Str
10 Templar Melee Str
11 Wizard Range Int
12 Warlock Range Int
13 Ranger Range Agi
14 Monk Melee Agi
15 Engineer Range Str
```

> 如果你是一个角色扮演游戏的粉丝，你会发现上面的内容很熟悉。随便说说。正在返回…

# 1.搜索特定的关键字

AWK 可用于搜索具有特定模式的线条:

```
**Syntax:** awk '/keyword/' filename
```

示例:

```
awk '/Melee/' jobclass.txt 
```

输出将是:

```
1 Warrior Melee Str
2 Knight Melee Str
4 Priest Melee Int
6 Assassin Melee Agi
9 Paladin Melee Str
10 Templar Melee Str
14 Monk Melee Agi
```

这是另一个例子:

```
awk '/Agi/' jobclass.txt
```

输出将是:

```
5 Hunter Range Agi
6 Assassin Melee Agi
13 Ranger Range Agi
14 Monk Melee Agi
```

# 2.仅显示特定的列

您也可以使用`print`来过滤输出，然后使用`$` 加上列号来指定列。

```
**Syntax:** awk '{print $columnnumber}' filename
```

示例:

```
awk '{print $2}' jobclass.txt
```

输出将是:

```
Warrior
Knight
Mage
Priest
Hunter
Assassin
Summoner
Bard
Paladin
Templar
Wizard
Warlock
Ranger
Monk
Engineer
```

对于多列，只需在打印时添加另一列，用逗号分隔，如下所示:

```
awk '{print $1,$4}' jobclass.txt
```

输出将是:

```
1 Str
2 Str
3 Int
4 Int
5 Agi
6 Agi
7 Int
8 Int
9 Str
10 Str
11 Int
12 Int
13 Agi
14 Agi
15 Str
```

不使用逗号将会删除空格:

```
awk '{print $1$4}' jobclass.txt
```

输出将是:

```
1Str
2Str
3Int
4Int
5Agi
6Agi
7Int
8Int
9Str
10Str
11Int
12Int
13Agi
14Agi
15Str
```

# 3.使用不同的字符作为分隔符自定义结果

您还可以自定义在输出中使用哪种分隔符:

```
**Syntax:** awk '{print $columnNumber "separator" $columnNumber}' filename
```

在下面的示例中，我们将使用逗号作为分隔符，而不是空格，这样它就可以用作 CSV 文件:

```
awk '{print $1","$2","$4}' jobclass.txt
```

输出将是:

```
1,Warrior,Str
2,Knight,Str
3,Mage,Int
4,Priest,Int
5,Hunter,Agi
6,Assassin,Agi
7,Summoner,Int
8,Bard,Int
9,Paladin,Str
10,Templar,Str
11,Wizard,Int
12,Warlock,Int
13,Ranger,Agi
14,Monk,Agi
15,Engineer,Str
```

# 4.组合搜索和列过滤

您通常会将搜索和列过滤结合起来解析所需的信息。

```
**Syntax:** awk '/keyword/ {print $columnNumber}' filename 
```

示例:

```
awk '/Int/ {print $2}' jobclass.txt
```

输出将是:

```
Mage
Priest
Summoner
Bard
Wizard
Warlock
```

下面是多列的另一个示例:

```
awk '/Int/ {print $2,$4}' jobclass.txt
```

输出将是:

```
Mage Int
Priest Int
Summoner Int
Bard Int
Wizard Int
Warlock Int
```

# 5.使用简单的条件语句

如前所述，AWK 也是一种编程语言。我们可以利用这一点，而无需进行复杂的编程。

```
**Syntax:** awk '{if ($columnNumber expression number) print $columnNumber}' filename
```

表情:

```
== Equal to!= Not equal to> Greater than< Less than>= Greater than or equal to<= Less than or equal to
```

在本例中，我们只想在第一列显示大于 9 的结果。那么将显示的列将仅仅是列 1 和列 2。

```
awk '{if ($1 > 9) print $1,$2}' jobclass.txt
```

输出将是:

```
10 Templar
11 Wizard
12 Warlock
13 Ranger
14 Monk
15 Engineer
```

这是另一个例子:

```
awk '{if ($1 == 9) print}' jobclass.txt
```

输出将是:

```
9 Paladin Melee Str
```

# 6.使用不同的字段分隔符

默认情况下，AWK 通过空格分隔列。但是您也可以使用可选命令`-F` 将其他字符设置为分隔符，然后指定分隔符。

注意:一些可选命令可能与其他 Linux 发行版或 Unix 类操作系统有所不同。

```
**Syntax:** awk -F 'separator' 'statement' filename
```

但是首先，我们需要改变我们的榜样。使用下面的文件并将文件保存到`jobclass2.txt`。

```
1,Warrior,Melee,Str
2,Knight,Melee,Str
3,Mage,Range,Int
4,Priest,Melee,Int
5,Hunter,Range,Agi
6,Assassin,Melee,Agi
7,Summoner,Range,Int
8,Bard,Range,Int
9,Paladin,Melee,Str
10,Templar,Melee,Str
11,Wizard,Range,Int
12,Warlock,Range,Int
13,Ranger,Range,Agi
14,Monk,Melee,Agi
15,Engineer,Range,Str
```

示例:

```
awk -F ',' '/Range/ {print $1,$2}' jobclass2.txt
```

输出将是:

```
3 Mage
5 Hunter
7 Summoner
8 Bard
11 Wizard
12 Warlock
13 Ranger
15 Engineer
```

# 7.使用和显示应该包含所有指定关键字的结果

逻辑 AND 使用`&&` 来声明。

```
**Syntax:** awk '/keyword/ && /keyword/' filename
```

示例:

```
awk '/Melee/ && /Str/' jobclass.txt
```

输出将是:

```
1 Warrior Melee Str
2 Knight Melee Str
9 Paladin Melee Str
10 Templar Melee Str
```

您也可以使用两个以上的关键字，如下例所示:

```
awk '/Melee/ && /Str/ && /1/' jobclass.txt
```

输出将是:

```
1 Warrior Melee Str
10 Templar Melee Str
```

以上示例显示了包含所有指定关键字的所有行。

# 8.使用“或”显示应该包含任何指定关键字的结果

逻辑 OR 使用 **||** 来声明。

```
**Syntax:** awk '/keyword/ || /keyword/' filename
```

示例:

```
awk '/Melee/ || /Str/' jobclass.txt
```

输出将是:

```
1 Warrior Melee Str
2 Knight Melee Str
4 Priest Melee Int
6 Assassin Melee Agi
9 Paladin Melee Str
10 Templar Melee Str
14 Monk Melee Agi
15 Engineer Range Str
```

你也可以像这个例子一样使用两个以上的关键字。

```
awk '/Melee/ || /Str/ || /1/' jobclass.txt
```

输出将是:

```
1 Warrior Melee Str
2 Knight Melee Str
4 Priest Melee Int
6 Assassin Melee Agi
9 Paladin Melee Str
10 Templar Melee Str
11 Wizard Range Int
12 Warlock Range Int
13 Ranger Range Agi
14 Monk Melee Agi
15 Engineer Range Str
```

上面的例子显示了具有任何指定关键字的所有行。

# 9.使用“not”显示除指定关键字之外的所有内容

逻辑不采用`**!**` 来声明。

```
**Syntax:** awk '!/keyword/' filename
```

示例:

```
awk '!/Range/' jobclass.txt
```

输出将是:

```
1 Warrior Melee Str
2 Knight Melee Str
4 Priest Melee Int
6 Assassin Melee Agi
9 Paladin Melee Str
10 Templar Melee Str
14 Monk Melee Agi
```

上面的例子显示了除了带有`Range`关键字的行之外的所有结果。

# 10.将 AWK 与其他 Linux 或 Unix 命令一起使用

AWK 可以与其他兼容命令结合使用，以处理其输出结果。

示例:

```
ls -l | awk '/file/ {print $1,$9}'
```

如果你经常使用`ls -l`命令，如果你尝试上面的命令，你会发现输出有很大的不同。

我希望 AWK 的这些实际应用能有所帮助。愿法典与你同在。