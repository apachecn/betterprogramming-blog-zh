# 用 Node.js 编写您的第一个 Shell 脚本

> 原文：<https://betterprogramming.pub/write-your-first-shell-script-with-node-js-c612da65b1e1>

## 在 Node.js 中从头开始编写 shell 脚本

![](img/7be1cf99291c9a1ead9bb3f9da79d218.png)

[活动创建者](https://unsplash.com/@campaign_creators?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在处理 JavaScript 应用程序时，Shell 脚本非常有用。如果你曾经使用过 [create-react-app](https://github.com/facebook/create-react-app) ，这个命令在开发 react 应用程序时会处理大量的开销。

其中一些开销包括编写脚本来执行或构建你的应用，比如`npm start` 或`npm run build`。这些是执行特定 shell 脚本的命令的很好的例子。

阅读完本文后，您将成功地在 Node.js 中从头开始编写了一个 shell 脚本。

我们要写的脚本很简单。给定命令`npm run emoji`，脚本将运行，`console.log`在我们的终端随机表情符号。

![](img/f7a7051e8e5e8a2c8f42ffc6ebc0188b.png)

首先，我们需要一个包含脚本将要执行的代码的文件。姑且称这个文件为`emoji.js`。一旦创建了文件，我们就可以开始编写脚本了。

如果你曾经看过你的`package.json`文件(作为一名 JavaScript 开发人员，你必须[熟悉这个文件，如果你还没有的话)，你可能已经看过脚本部分。](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm/)

这由一个键表示，写成字符串“scripts”，指向一个对象值。这个对象包含了你的项目中使用的各种脚本(比如 `npm start`)。

下面是一个使用`create-react-app`后脚本在`package.json`文件中的键值对的例子:

```
"scripts":{
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
}
```

这个对象是我们的新脚本将居住的地方。在您的`package.json`文件中，在脚本对象中写下下面一行:

```
"emoji": "node emoji.js" 
```

现在，您的脚本部分应该如下所示:

```
"scripts":{
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "emoji": "node emoji.js"
}
```

在 scripts 对象中，键代表将执行值的命令。所以当我们键入命令`npm run emoji`时，它会执行命令`node emoji.js`。

在 Node 中，键入`node + filename.js`将在命令行中运行该 JavaScript 文件。如前所述，我们可以通过键入`npm run emoji`来运行这个脚本。但是在运行我们的脚本之前，让我们安装我们的依赖项。

在命令行中写入这些行来安装依赖项:

```
npm install node-emojinpm install random-emoji
```

我们将使用两个依赖关系，[节点表情符号](https://www.npmjs.com/package/node-emoji)和[随机表情符号](https://www.npmjs.com/package/random-emoji)。Node-emoji 是一个 npm 包，为 Node.js 项目提供表情支持。

Node-emoji 有一个接受字符串的 get 函数。这个字符串是表情符号的简称。如果你想得到心形表情符号，你可以这样写:

```
var emoji = require('node-emoji');console.log(emoji.get('heart'));
```

这是我们终端的结果:

```
C:\project-directory> npm run emoji❤️
```

你也可以这样写:

```
var emoji = require('node-emoji');console.log(emoji.get(':heart:'));
```

这种格式是传统的表情符号减价，受到许多在线主机的支持，如 GitHub。这将为我们的下一个依赖项 random-emoji 提供便利。

Random-emoji 以传统的 markdown 格式返回一个字符串，包括开头和结尾的冒号。我们将用来返回这个短代码字符串的 random-emoji 函数非常简单，就是 random。

如果你想返回一个随机的表情符号短码，你可以这样写:

```
var emoji = require('emoji-random');console.log(emoji.random());
```

这是回报的样子:

```
C:\project-directory> npm run emoji:squirrel:
```

我们可以结合这些依赖关系及其相关功能来创建我们独特的脚本，记录随机的表情符号，作为表情符号本身编写，而不仅仅是短代码。

为此，你需要首先调用 random-emoji 的 random 函数。一旦获得了随机的表情符号短码，就可以将该字符串传递给 node-emoji 的 get 函数。这将检索表情本身。

要在你的终端中看到最终的表情符号，只需`console.log`结果。这是我写的代码，最后一行有一些格式来表示表情符号短码(去掉冒号)和表情符号本身。

```
var randEmoji = require('emoji-random'),
    emoji = require('node-emoji');var randString = randEmoji.random();var finalEmoji = emoji.get(randString);console.log('Here is the '+ randString.split(':')[1] + 'emoji: '+ finalEmoji);
```

下面是记录的结果:

```
C:\project-directory> npm run emojiHere is the video_game emoji: 🎮
```

现在，虽然您仍然可以通过键入命令`npm run emoji` *，*随时运行这个脚本，但我们可以更进一步，每次调用其他脚本时都调用这个脚本。

假设我们有一个 React 应用程序，我们想在每次尝试启动我们的应用程序时在我们的终端中生成一个随机的表情符号(比功能更有趣)，我们可以在我们的启动脚本中调用我们的新表情符号脚本。

无论何时启动应用程序，您都需要调用 node emoji.js:

```
"start": "react-scripts start & npm run emoji"
```

通过结合我们的 start 和 emoji 脚本，我们可以在每次运行`npm start` *时执行 emoji 脚本。*而不必在我们的命令行*中写`npm run emoji`或者`node emoji.js` 。*

您在`package.json`中的最终脚本部分应该如下所示:

```
"scripts": {
    "start": "react-scripts start & npm run emoji",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject", 
    "emoji": "node emoji.js"
 }
```

通过将这个包含在你的`package.json`文件中，每次你启动你的应用程序时，你也会生成一个随机的表情符号。虽然这不是最有用的脚本，但希望现在您对脚本以及如何实现自己的脚本有了更好的理解。

如果你喜欢这篇文章或者需要澄清任何提到的事情，请在评论中告诉我。

[*点击此处，将您的免费媒体会员升级为付费会员*](https://matt-croak.medium.com/membership) *，每月只需 5 美元，您就可以收到各种出版物上数千名作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！*

# 参考

[](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm/) [## Node.js 和 npm 中 Package.json 的基础知识

### package.json 文件是 Node.js 生态系统的核心，是理解和使用 Node.js 的基础部分

nodesource.com](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm/) [](https://ponyfoo.com/articles/choose-grunt-gulp-or-npm#using-shell-scripts-in-npm-tasks) [## 选择:咕噜，吞咽，或 npm？

### 作为第一步，让我们讨论一下 Grunt 擅长的地方。Grunt 最好的一个方面就是它的易用性。它使…

ponyfoo.com](https://ponyfoo.com/articles/choose-grunt-gulp-or-npm#using-shell-scripts-in-npm-tasks) [](https://www.npmjs.com/package/node-emoji) [## 节点表情符号

### node.js 项目的简单表情支持

www.npmjs.com](https://www.npmjs.com/package/node-emoji) [](https://www.npmjs.com/package/random-emoji) [## 随机表情符号

### 随机表情符号的味道:运气、俳句等。

www.npmjs.com](https://www.npmjs.com/package/random-emoji) [](https://www.webfx.com/tools/emoji-cheat-sheet/) [## GitHub、Basecamp 和其他服务的表情符号备忘单

### GitHub、Basecamp、Slack 等服务使用的表情代码。可搜索。表情符号的含义。

www.webfx.com](https://www.webfx.com/tools/emoji-cheat-sheet/)