# 如何编写 JavaScript 代码来创建和更新 Changelog

> 原文：<https://betterprogramming.pub/how-to-write-javascript-code-to-create-and-update-a-changelog-4f57f2bca914>

## 不再有生成器、日志或依赖——现在是 JavaScript！

![](img/7e64ee6fbe5cd32d567555746d5a263d.png)

照片由[格雷格·拉科齐](https://unsplash.com/@grakozy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你读了我的[上一篇关于如何编写 python 脚本来创建和更新 changelog 的文章](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab)，或者[上一篇关于在 bash 中编写 changelog 脚本的文章](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab) *在*之前，你可能也会对这篇文章感兴趣。目标是增加对多种语言的支持，这样开发人员就不必只选择一种语言，而可以选择最符合他们当前使用的堆栈的语言。

我添加了 [JavaScript 支持](https://github.com/macro6461/changelog-python)来扩展我的[原始 changelog 脚本](https://github.com/macro6461/changelog-bash)给偏爱 JavaScript 的工程师。现在，您可以对 JavaScript 使用相同的脚本逻辑。如果您是一名 JS 开发人员，并且想要为您的 changelog 创建/生成添加更多的定制，那么现在有了这个新增的支持，您可以更容易地做到这一点。

因为它与我上两篇关于 Bash 和 Python 的文章的逻辑相同，所以我不会在功能方面做太深入的讨论。与 Python 上的文章一样，这篇文章将强调语法差异。

首先，创建一个`changelog.js`文件来运行脚本的逻辑。您可以通过运行`chmod +x changelog.js`使该文件可执行。这样你就可以从命令行运行`./changelog.js`,或者在另一个脚本中添加`./changelog.js`来包含它。

接下来，我们需要我们的项目版本与我们的 changelog 同步。这可能是 JavaScript 代码中最复杂的部分。您可以做两件事之一:

1.  找到一个可以用来在 JavaScript 文件中执行`git describe --long`的包(可能类似于[这个](https://www.npmjs.com/package/git-describe/v/2.0.0))。
2.  做一个纯粹主义者，用你自己的代码来做所有的事情！如果这是你要去的路线，我们有几个步骤。

首先，我们必须`require`所谓的`child_process`，然后在那个子进程上运行`exec`。[这个方法](https://www.javatpoint.com/nodejs-child-process)允许我们在控制台中运行命令并缓冲输出。这是我们的第一行代码。

```
const exec = require('child_process').exec;
```

接下来，我们需要编写一个函数，运行一个接受`"git describe --long"`作为参数的子执行，以及一个回调函数。下面是我们的函数。

```
const result = (command, cb) => {
  var child = exec(command, (err, stdout, stderr) => {
    if(err != null){
      return cb(new Error(err), null);
    }else if(typeof(stderr) != "string"){
      return cb(new Error(stderr), null);
    }else{
      return cb(null, stdout);
   }
  });
  return child
}
```

这基本上是一个包装器，它将执行传递的命令和回调函数。这个包装器还提供了初步的错误处理。我们剩下要做的就是编写一个实际执行这个序列并处理结果的函数。我们称这个函数为`getTaggedVersion`。

这是代码。

```
const getTaggedVersion = () => {
  result("git describe --long", (err, response) => {
    if(!err){ 
      console.log(response);
    }else {
      console.log(err);
    }
  });
}
```

这有点复杂，但是它可以让您不必安装和配置依赖项！现在，到我们的脚本的其余部分。

在`changelog.js`中有我们使用字符串插值的地方。这在 JavaScript 中的实现方式与在 Bash 或 Python 中略有不同。Bash 字符串插值见下文。

```
name="Matthew Croak"
introduction="My name is $name"
echo $introduction
> "My name is Matthew"
```

最后，这里是 JavaScript 的字符串插值。

```
name="Matthew Croak"
introduction=`My name is ${name}`
console.log(introduction)
> "My name is Matthew"
```

字符串插值对脚本很重要，因为我们将从提供的字符串写入和重写 changelog 文件。这些字符串将包含更新的信息，例如今天的日期和包的版本。

接下来，让我们写我们的`init`函数。这个函数将被用来决定我们是否想要创建一个新的 changelog 或者简单地更新一个现有的。在 JavaScript 中，`[fs](https://nodejs.dev/learn/the-nodejs-fs-module)` [模块](https://nodejs.dev/learn/the-nodejs-fs-module)使得文件处理相对简单。我们可以从`fs`模块导入`existsSync`并检查提供的路径是否存在。见下文。

```
const {existsSync} = require('fs');
const path = './CHANGELOG.md'const init = () =>{
  try {
    if (existsSync(path)) {
      newChangelogItem()
    } else {
      newChangelog()
    }
  } catch (err){
    console.log(err)
  }
}
```

接下来，让我们回顾一下如何读写我们的 changelog 文件。在 Bash 中，您可以这样读取文件:

```
while read line; do           
  # code performed for each line
done < CHANGELOG.md
```

在 JavaScript 中，我们使用`readFileSync`到*读取*提供的文件，使用`writeFileSync`到*将*写入提供的文件。这两种功能都由`fs`模块提供。`readFileSync`将获取一个提供的文件，并将其转换为字符串。然后，我们可以获取这个字符串，并在每一个新行中使用`split`(`/n`)。

这样，我们可以创建一个要迭代的行数组。见下文。

```
const fileData = readFileSync("CHANGELOG.md", { encoding: "utf8" });
const fileDataArray = fileData.split("\n");
```

`writeFileSync`看起来很相似，但是它需要一个额外的参数(在文件名和编码数据之间)。第三个参数是提供的将被写入文件的字符串。见下文。

```
writeFileSync("CHANGELOG.md", newFileData, { encoding: "utf8" });
```

在我们继续讨论`readAsync`和`writeAsync`之前，让我们再看一遍`fs`模块提供的功能:`appendFile`。

顾名思义，我们可以使用`appendFile`将文件数据追加到文件中。通过提供文件名、文件数据(字符串形式)和回调函数，我们可以轻松地创建 changelog 文件。见下文。

```
const newChangelog = () => {
  appendFile("CHANGELOG.md", changelogStart, (err) => {
    if (err) throw err;
    console.log('Changelog is created successfully.');
  })
}
```

这个函数将创建一个新文件(`CHANGELOG.md`)，用`changelogStart`(在 [Github](https://github.com/macro6461/changelog-js/blob/main/changelog.js) 上的 changelog.js 文件中可用的字符串)填充它。现在我们已经创建了将为我们创建一个 changelog 的函数，如果它不存在，我们可以继续使用我们的函数向现有的 changelog 添加一个新的 changelog 项目！

现在，在我们进入行插入代码之前，让我们看看我们需要什么代码来确定我们*是否应该*添加一个项目。记住，我们的变更日志条目对应于一个项目版本*和*日期。如果我们已经有一个当前版本和日期的 changelog 项目，我们不需要创建一个新的。

下面是我们将用来检查我们是否需要一个新项目的代码。

```
const newChangelogItem = () => {
  if (checkIfItemExists(item)){
    console.log(`Changelog item already exists for \n   ${item}`)
  } else {
    writeChangelog()
  }
}
```

我们一会儿会讲到`writeChangelog`，但首先，这里是`checkIfItemExists`。

```
const checkIfItemExists = (str) => {
  const contents = readFileSync('CHANGELOG.md', 'utf-8');
  return contents.indexOf(str) > -1;
}
```

该功能利用了`readFileAsync`。这将生成一个字符串版本的 changelog。一旦它是一个字符串，我们可以使用检查我们的 changelog 项目的索引。如果它大于-1，那么它存在，我们不需要添加一个新的。否则，添加一个新的。简单，不是吗？

既然我们正在处理是否可以/应该添加一个新的 changelog 条目，那么让我们来看看如何实际添加一个。为此，我们将利用`readFileAsync`和`writeFileAsync`。我们将把我们的文件转换成一个行的数组(如前所述)，然后迭代这个数组。在迭代我们的代码行时，我们寻找将要插入新项目的代码行。这条线用`"## [Unreleased]"`表示。

我们可以再次使用`indexOf`来检查这一点。如果`fileDataArray[i].indexOf(“## [Unreleased]”) > -1`那么我们知道*那*就是我们插入项目的那一行。如何在 JavaScript 中插入我们的项目？利用`splice`。

允许我们在数组中添加新的元素来代替现有的元素。我们可以简单地在我们的第`“## [Unreleased]”`行之后`splice`我们的新项目到我们的数组中。见下文。

```
iterateArr = [...fileDataArray)
for (var i = 0; i < iterateArr.length; i++){
  if (fileDataArray[i].indexOf("## [Unreleased]") > -1){
    fileDataArray.splice(i + 1, 0, `\n${item}\n### Added\n- ADD CHANGE HERE!`)
     const newFileData = fileDataArray.join("\n");
     writeFileSync("CHANGELOG.md", newFileData, { encoding: "utf8" })
     break
  }
}
```

使用扩展运算符从`fileDataArray`创建`iterateArr`。我们这样做是因为我们不想迭代我们正在操作的数组。一旦我们把新的条目放到了行的数组中，我们就可以把它们放到一个字符串中。然后我们可以将这个字符串提供给`writeFileAsync`并将其写入我们的`CHANGELOG.md`文件。

## 你应该用哪一个？

假设这一代 changelog 现在支持 Bash、Python 和 JavaScript，你应该使用哪一个？我个人觉得 Python 是首选。我觉得 Bash 的学习曲线比 Python 宽一点(至少对我来说)，虽然 Bash 的代码行更少，但我在调试方面更加努力。

JavaScript 是我的首选语言(我有偏见，因为我多年来一直专业地使用 JavaScript)，但是对于这个用例，我发现我必须编写更多的配置代码。虽然`fs`模块为文件管理和操作提供了大量内置逻辑，但我不得不编写单独的函数来支持 git 命令的执行*和*，我发现为了开始遍历文件并添加文本，还需要更多的步骤。

但是如果你是一个 JavaScripter 爱好者(？)现在，您已经有了一个*纯*脚本，可以整合到您的应用程序中来生成一个变更日志*和*添加变更日志项目。它还在格式和变更日志内容方面为您提供了灵活性*而不需要*生成器、日志或其他依赖项。

完整代码在 [GitHub](https://github.com/macro6461/changelog-js) 上。

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*您也可以通过电子邮件* *订阅，每当我发布新内容时，您都会收到通知！*

谢谢你！

# 参考

[如何编写 Bash 脚本来创建和更新变更日志](/how-to-write-a-bash-script-to-create-and-update-a-changelog-de4df98985ab)

[GitHub—macro 6461/changelog-python:生成并更新变更日志的 Python 脚本](https://github.com/macro6461/changelog-python)

[GitHub—macro 6461/changelog-bash:生成和添加 changelog 项目的纯 bash 脚本](https://github.com/macro6461/changelog-bash)

[git-描述](https://www.npmjs.com/package/git-describe/v/2.0.0)

[Node.js 子进程—Java point](https://www.javatpoint.com/nodejs-child-process)

[主宏 6461/changelog-js 处的 changelog-js/changelog . js](https://github.com/macro6461/changelog-js/blob/main/changelog.js)

[array . prototype . splice()—JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

[GitHub—macro 6461/changelog-js:用于生成和更新变更日志的 JavaScript 代码](https://github.com/macro6461/changelog-js)