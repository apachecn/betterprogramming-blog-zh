# 在 Python 中使用全局变量的替代方法

> 原文：<https://betterprogramming.pub/alternatives-to-using-globals-in-python-a3b2a7d5411b>

## 什么时候使用它们，什么时候放下它们

![](img/f9d552b82d766898860dd73e4ef5c8fb.png)

照片由 [Fernando @dearferdo](https://unsplash.com/@dearferdo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/globe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我绝不是 Python 方面的专家，但是经过几年或多或少专门用它开发(JavaScript，我还是爱你！)，我已经开始觉得可以写这个了。

这篇短文是关于全局变量以及如何使用(或不使用)它们的。但最重要的是，它是关于它们的替代品——一些通常被忽略的东西。

# 什么是全局？

全局变量就是一个可以从程序中任何地方访问的变量。考虑这些例子:

```
**INIT = False**def run():
    **global INIT**
    print('Will Run')
    if **INIT**:
        print( 'Already initiated' )
    if not **INIT**:
        init()def init():
    **global INIT**
    **INIT** = True
    print('Will Init')run()
run()**OUTPUT:***Will Run
Will Init
Will Run
Already initiated*
```

`run()`和`init()`函数都可以通过`global` 关键字访问`INIT`变量。这允许以下逻辑:运行`init()`一次，将`INIT`变量设置为`True`，然后检查它是否已经运行(通过`INIT`变量)。如果有，就不要再运行了。

有很多例子表明，全球化似乎是一个完美的组合。考虑另一个例子——我们只需要在各种函数中引用一种颜色或一些其他变量:

```
GUI_THEME = 'BabyBlue'
GUI_FONT = 'Lobster'def makeGUI():
    **global GUI_THEME,GUI_FONT**
    print('making GUI in: ' + GUI_THEME)
    print('with FONT: ' + GUI_FONT)def changeTheme():
    **global GUI_THEME**
    GUI_THEME = 'AwesomeOrange'def changeFont():
    **global GUI_FONT**
    GUI_FONT = 'Merryweather'makeGUI()
changeTheme()
changeFont()
makeGUI()**OUTPUT:***makig GUI in: BabyBlue
with FONT: Lobster
makig GUI in: AwesomeOrange
with FONT: Merryweather*
```

另请注意，即使没有将变量声明为全局变量，您仍然可以读取它。所以在这种情况下，它可以被视为一个常数:

```
GUI_THEME = 'constantGrey'def makeGUI():
    print('making GUI in: ' + GUI_THEME)
    # Try to change it:def changeGUI():
    GUI_THEME = 'variableGrey' **# Local variable**
    print('making GUI in: ' + GUI_THEME)makeGUI()
changeGUI()
makeGUI()making GUI in: constantGrey
making GUI in: variableGrey
making GUI in: constantGrey But it fails when trying to change it, note how the second makeGUI() is still using the original theme and the changeGUI was only valid locally. 
```

```
👋👋 Hi there 👋👋 all my content is free for Medium subscribers, if you are already a subscriber I wanted to say thank you ! 🎉 If not and you are considering subscribing, you can use my membership referral link, you will be supporting this and other high quality content, Thank you !**⭐️⭐** [**Subscribe to Medium !**](https://k3no.medium.com/membership) **⭐️⭐️**
```

# 为什么不用它们呢？

如果有人叫你不要使用全局变量，而你没有得到任何解释(或者一个令人费解的答案)，这一节是为你准备的。以谷歌的风格指南为例:

```
**2.5 Global variables:**
Avoid global variables.**WHY ?:**Has the potential to change module behavior during the import, because assignments to global variables are done when the module is first imported.🤔🤔🤔
```

# 何时使用它们

我往前跳了一点，因为之前的风格指南接着说:

```
While they are technically variables, module-level constants are permitted and encouraged.External access must be done through public module-level functions
```

困难在于:在构建原型时，除了使用其他库中的模块之外，我通常不会一开始就编写模块。因此，在启动程序或脚本时使用全局变量似乎非常好(甚至更方便)。

然后随着它变得更加复杂，你可以把它们分成内部常量和项目范围的变量…但是我们正在前进。让我们首先举例说明为什么在一个模块中使用全局变量可能是一个坏主意——这只是一种[解释](https://stackoverflow.com/questions/16258514/using-global-variable-has-the-potential-to-change-module-behavior-during-the-imp?rq=1):

```
*FILE: module_01.py*GLOBAL_MODULE_VAR = **False**def changeGlobal(): #*<< Public access*
    global GLOBAL_MODULE_VAR
    GLOBAL_MODULE_VAR = True
```

在代码的其他地方:

```
import module_01 as M1print(M1.GLOBAL_MODULE_VAR)
# **False**, all good here.M1.changeGlobal() *#<< Public access*
print(M1.**GLOBAL_MODULE_VAR)
# True**, still good, but...import module_01 as M2print(M2.GLOBAL_MODULE_VAR)
**# Still True, Unless you wanted False !, and who is keeping track of these changes ?**M1.GLOBAL_MODULE_VAR = False
# Direct assignment, works, but can get messy.
```

请注意，如果这是您想要的，这种行为没有什么错，但最有可能的是，您希望您的全局变量(更重要的是，您的状态)是一个只有少数函数可以触及的引用，而不是一些更可变和开放的东西。

还有一种情况是根据惯例组织你的代码，使其更具可读性。例如，直接赋值使代码难以理解。

# 我们应该用什么来代替呢？

## 类别:

OOP 方法:这是第一个类形式的例子。

```
**# Create class**
class Init:
    def __init__(self, init_: bool):
        self.init = init_**# Create Instance of the class**
init_ = Init(False)def run():
    print('Will Run') if init_.init:
        print( 'Already initiated' )
    if not init_.init:
        init()def init():
    init_.init = True
    print('Will Init')run()
run() **Output:**Will Run
Will Init
Will Run
Already initiated
```

第二个例子是:

```
**class GUI:
    def __init__(self):
        self.GUI_THEME = 'BabyBlue'
        self.GUI_FONT = 'Lobster'****GUI_ = GUI()** # Can also use initialization argumentsdef makeGUI():
    print('making GUI in: ' + GUI_.GUI_THEME)
    print('with FONT: ' + GUI_.GUI_FONT)def changeTheme():
 **GUI_.GUI_THEME = 'AwesomeOrange'**def changeFont():
 **GUI_.GUI_FONT = 'Merryweather'**makeGUI()
changeTheme()
changeFont()
makeGUI()**OUTPUT:***making GUI in: BabyBlue
with FONT: Lobster
making GUI in: AwesomeOrange
with FONT: Merryweather*
```

这里的缺点是有一些代码和复杂性开销，但这似乎是一个流行的选择。

## 全局变量(字典、列表和集合):

(点击了解更多关于可变[的信息。)](https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747)

简而言之，*可变变量*的行为就像没有 global 关键字的全局变量——需要注意的是，它们需要包装在一个具有不同可读性的列表、集合或字典中。

```
**_G = {'INIT': False }**
# Mutable Dictdef run():
    print('Will Run')
    if _G['INIT']:
        print("Already initiated")
    else:
        init()def init():
 **_G['INIT'] = True**
    print('Will Init')run()
run()
print(_G)**OUTPUT:**Will Run
Will Init
Will Run
Already initiated
**{'INIT': True}**
```

您也可以使用列表:

```
**_INIT = [False]**
# Mutable List:def run():
    print('Will Run')
 **if _INIT[0]:**
        print("Already initiated")
    else:
        init()def init():
 **_INIT[0] = True**
    print('Will Init')run()
run()
**print(_INIT)****OUTPUT:**Will Run
Will Init
Will Run
Already initiated
**[True]**
```

你甚至可以用一套。集合是无序的，所以你需要想出额外的逻辑。

```
**_INITSET = {'notInititated'}**
# MUTABLE SETdef run():
    print('Will Run')
 **if ("Inititated" in _INITSET):**
        print("Already initiated")
    else:
        init()def init():
    **_INITSET.remove("notInititated")**
 **_INITSET.add("Inititated")**
    print('Will Init')run()
run()
print(_INITSET)**OUTPUT:**Will Run
Will Init
Will Run
Already initiated
**{'Inititated'}**
```

然而，字典似乎是可读性更强、最划算的选择:

```
**_GUI = {'THEME_COLOR': 'BabyBlue', 'THEME_FONT': 'Lobster'}**def makeGUI():
    print('makig GUI in: ' + **_GUI['THEME_COLOR']**)
    print('with FONT: ' + **_GUI['THEME_FONT']**)def changeThemeColor():
 **_GUI['THEME_COLOR'] = 'AwesomeOrange'**def changeThemeFont():
 **_GUI['THEME_FONT'] = 'Merryweather'**makeGUI()
changeThemeColor()
changeThemeFont()
makeGUI()**OUTPUT:**makig GUI in: BabyBlue
with FONT: Lobster
makig GUI in: AwesomeOrange
with FONT: Merryweather
```

至于使用哪种方法，类还是变量，我认为这取决于所表示的变量有多复杂。

如果只是一个变量列表，一个可变的就可以了。如果需要做一些处理(或者你的老板要求这样做)，一个类可能会更好。

# **外部访问**

在总结之前，我们需要看看这两种方法在导入模块时是如何工作的:

```
*FILE: module_02.py*class ModuleVARS:
    def __init__(self, init_: bool):
        self.GLOBAL_MODULE_VAR = init_*Add class methods to suit your needs...*
```

用法:

```
import module_02 as M2# New Instance:**GLOBALMODULEVARS = M2.ModuleVARS(True)**
print(GLOBALMODULEVARS.GLOBAL_MODULE_VAR)#Truedef changeGlobalVar():
    **GLOBALMODULEVARS.GLOBAL_MODULE_VAR = False**
    print(GLOBALMODULEVARS.GLOBAL_MODULE_VAR)changeGlobalVar()#False
```

并且使用可变的:

```
*FILE: module_03.py*_GUI = {'THEME_COLOR': 'BabyBlue', 'THEME_FONT': 'Lobster'}*Add class methods to suit your needs...*
```

用法:

```
import module_03 as M3print(**M3._GUI['THEME_COLOR']**)
print(**M3._GUI['THEME_FONT']**)#BabyBlue
#Lobsterdef changeTheme():
    M3._GUI['THEME_COLOR'] = 'AwesomeOrange'
    print(M3._GUI['THEME_COLOR'])def changeFont():
    M3._GUI['THEME_FONT'] = 'Merryweather'
    print(M3._GUI['THEME_FONT'])changeTheme() #AwesomeOrange
changeFont() #Merry-weather
```

同样，使用什么取决于代码试图模拟的数据结构。如果它是一个引用，mutables 可能会更好，但是如果它有一些内部逻辑，一个类可能会更适合。

## **结论**

在代码中有很多方法可以做同样的事情，Python 也不例外。有些人比其他人更适合，但通过了解几个路径，你可以选择一个有意义的。

在程序或脚本中使用可变引用可以通过几种方式实现。这里我们看到了全局变量和类。和可变的，scand 每一个都有它的优点和缺点，可能更适合某些项目。

在程序的发展过程中，您可能会在不同的时间使用这三种方法，或者为了团队的一致性和/或可读性，选择一种方法。

TL；dr:用字典(参考)或者类(参考+逻辑)代替。搭建原型时使用全局变量。

感谢阅读。