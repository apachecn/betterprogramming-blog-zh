# 让我们用 React 和 TypeScript 和钩子来构建一个井字游戏

> 原文：<https://betterprogramming.pub/lets-build-a-tic-tac-toe-game-using-react-with-typescript-and-hooks-321f78d8d1e0>

## 了解如何在不到 10 分钟的时间内创建一个小游戏

![](img/625821e0d53e63c0e34f937b9ec1a1f5.png)

米歇尔·亨德森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

我一直认为，学习一件事情的最好方法是通过建立一个项目来付诸实践。虽然我已经熟悉了 React 和 2019 年推出的钩子的辉煌概念，但自 2018 年以来，我大部分时间都在与 Vue 合作。我已经有一段时间没用 React 和 Hooks 做过什么了。

为了检查我是否还能使用 React 构建一些东西，我决定尝试构建一个井字游戏。虽然这看起来像是一个非常基础的项目，但是这是一个练习 React 和钩子概念的绝佳机会。

如果你对 React、Hooks 和 TypeScript 一无所知，阅读下面的两篇文章会是一个好主意:

*   [反应过来的失踪介绍](https://medium.com/javascript-scene/the-missing-introduction-to-react-62837cb2fd76)
*   [5 分钟学会打字稿](https://medium.com/free-code-camp/learn-typescript-in-5-minutes-13eda868daeb)

说了这么多，让我们动手开始构建我们的应用程序吧。

# 基本布局

我们将首先使用 create-react-app 或 [Vite](https://vitejs.dev/) 搭建一个新的 React 应用。

如果你还不知道 Vite，它是由npx create-react-app tic-tac-toe --template typescript
# if you prefer vite
npx init @vitejs/app tic-tac-toe --template react-ts

如果你不想使用任何本地资源，一个好主意是使用 [CodeSandbox](https://codesandbox.io/) 。他们有一个非常好的类似 VS 代码的在线编辑器和一个带热重载的开发服务器。

在你选择了方法并且它完成了依赖项的安装之后(在 create-react-app 和 Vite 的情况下)，在你最喜欢的编辑器中打开井字游戏(我建议 VS 代码)。

让我们首先创建一个包含三个文件的`pages`文件夹:`Start.tsx`、`Game.tsx`和`Finished.tsx`。我将使用这些名称，因为它们对我有意义，但是您可以选择任何您想要的名称。

# 起始页

让我们从创建起始页开始。我们将有一个标题，一个允许用户输入两个玩家名字的表单，以及一个只有在两个输入都被填充时才会被激活的开始按钮。

正如我前面说过的，我们将在这个项目中使用 TypeScript，所以为了从父组件接收道具，我们需要声明我们的接口。TypeScript 接口是组件和父组件之间的基本契约，它描述了哪些属性可用以及每个属性的类型。在我们的例子中，我们将得到的唯一道具是`handleStart`方法。

在这个例子中，我将把需要的接口放在同一个组件文件中，但是在更大的项目中，一个更好的想法是将每个接口放在一个`src/types`文件夹中的单独文件中。

如果你知道 React 是如何工作的，这是非常标准的。我们有一个带有两个输入的表单:`useState`用于控制它们的值(通过在每个输入上使用 value 和`onInput`属性)和一个开始按钮。只有当两个输入都被填充时，按钮才被激活，当它满足这个条件时，props 接收的一个名为`handleStart`的方法被调用，传递一个带有两个输入值的数组。我们将在后面的`App`组件中负责构造这个函数。

我们在启用/禁用按钮的函数中使用了`useMemo`，以避免在`users`数组不变的情况下重新创建按钮。

# 完成的一页

这个页面会非常简单。它只会显示获胜玩家的名字或平局信息(以防没有方块和没有人获胜)以及重新开始游戏的按钮。

我们必须定义负责告诉父母道具可用的接口。在这种情况下，我们将接收获胜者的名字(如果还没有人获胜，可以是字符串或`null`)和重启方法。

# 游戏页面

这将是主页面，将提出董事会，并让玩家点击标记 X 或 o 的方块。

您可能已经猜到，这个页面将负责绘制组成它的九个`Square`组件。每个`Square`组件将负责在板上画出相应的位置并监听点击事件。

首先，让我们构建我们的`Square`组件。创建一个包含`Square.tsx`文件的`src/components`文件夹。

该组件将有三个在接口中定义的属性:定义棋盘上位置的数字(索引)、值(X 或 O)和 click 事件处理程序。click 事件将接收方块索引，以便控制棋盘的函数知道哪个棋子被点击了。我们将使用一些样式，使按钮的宽度和高度为 100 像素，字体大小为 46 像素。

下一个是`Square`家长，游戏页面。该页面将从父页面接收具有九个字符串位置(空、X 或 O)的棋盘数组和`clickHandler`(将传递给九个方格中的每一个)。然后它将使用`map`为每个棋盘元素渲染一个正方形。

为了使它看起来像一个井字游戏棋盘，我们将使用 CSS Grid 自动使布局为三列 x 三行。

# 应用程序组件

在`App`组件中，我们必须有某种逻辑来知道游戏的确切位置(开始、游戏或获胜页面)以及游戏的业务逻辑。

既然我们有钩子，我们可以将游戏的业务逻辑委托给一个钩子(`useTicTacToe`是一个好名字)，让组件只负责绘制所需页面的逻辑。

我们尚未创建的`useTicTacToe`钩子必须返回一些信息:

*   游戏的状态(例如，创建、开始和结束)
*   棋盘本身，这样它就可以呈现在屏幕上
*   获胜玩家的名字
*   重启功能(清空棋盘、玩家和赢家)
*   开始功能(将设置玩家的名字)
*   click 事件处理函数

此外，它需要有一个状态来告诉玩家是否已经开始游戏。通过“开始”游戏，我的意思是他们已经填写了他们的名字并点击了开始。

既然我们已经得到了我们需要的一切，让我们开始编码:

到目前为止，这相当简单，因为我们将所有的业务逻辑委托给我们接下来要构建的`useTicTacToe`钩子。

# 钩子

我决定使用一个钩子来收集使游戏正常运行所涉及的所有业务逻辑，这是保持关注点分离的好方法。作为一个额外的奖励，它可以用在其他项目中，这些项目选择用一种不同的(也更漂亮的)方式来渲染游戏。

正如我们前面所说的，这个钩子必须返回关于游戏状态、棋盘、赢家(如果游戏有)的信息，以及重启、启动(注册玩家)和处理点击事件的方法。

我认为如果我们有三个状态(棋盘、赢家和状态)，我们前面提到的三个函数，以及一个`useEffect`钩子来检查当棋盘改变时某人是否已经赢了，那就很好了。为了使获胜检查简单，我们将有一个获胜位置的数组，然后该函数将检查这些位置中是否有任何位置填充了相同的值。最后，如果棋盘改变了，没有人赢，所有的位置都有人了，我们就平手了(状态改变为完成，但赢家仍然是`null`)。当你看到代码时，你会更好地理解它。

至于组件，我们必须定义一个接口来告诉任何使用钩子的组件它们需要什么样的属性来与钩子交互。在我们的例子中，我们的接口将有棋盘(字符串数组)、状态(字符串)、获胜者的名字(字符串或`null`，如果还没有人获胜)，以及三个方法(`handleClick`、`handleRestart`和`handleStart`)。

因此创建一个`src/hooks`文件夹，并在其中创建一个`useTicTacToe.ts`。

很简单，对吧？只需一些基本的 JavaScript，我们就可以将井字游戏业务逻辑封装在一个小的可重用钩子中。

# 结论

原来如此。我们用 React、TypeScript 和 Hooks 构建了这个小小的井字游戏。

如果你想玩已经构建好的应用程序(也许通过添加一些 CSS 让它不那么难看)，我在 CodeSandbox 上留下了一个工作应用程序:

我的 [GitHub](https://github.com/n0n3br/react-tic-tac-toe) 账户上也有结果代码。

如果你坚持到了最后，我希望你学到了有用的东西。下一篇文章再见。