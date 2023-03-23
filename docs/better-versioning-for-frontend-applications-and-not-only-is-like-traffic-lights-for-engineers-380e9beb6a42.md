# 一种更好的前端应用程序版本控制技术

> 原文：<https://betterprogramming.pub/better-versioning-for-frontend-applications-and-not-only-is-like-traffic-lights-for-engineers-380e9beb6a42>

## 健康的方法

![](img/d024dec0b13e517e936f36d290aa6af9.png)

## 我们是如何来到这里的。

我们都曾在某个时候经历过。应用程序版本被简化为整数，并随着每次发布而增加…或者不增加。当在一个更大的组织中工作，并且多个团队可能参与一个代码库时，随机性和缺乏规则没有帮助。幸运的是，当人们真正关心的时候，解决问题是很容易的。

回到 2016 年，在我为 Yell.com 完成了一个新的架构之后，我开始思考我们的应用程序版本的最佳方式是什么。如何只用一个数字来体现发布严肃性？这个想法来自于我的前老板史蒂夫·沃克曼，他想尝试一下 **SemVer 2.0** 。尽管没有人愿意手动管理它，但团队还是立即接受了它。因此，我们将构建过程脚本放在适当的位置，以根据我们的需求自动化版本，并推动正确的存储库以避免冲突和错误。我们的新方法多年来运行良好，帮助我们开发了更好的 git 分支策略、发布说明管理和健壮的部署管道。

我总是试图避免为工程团队制定严格的规则，但这次是个例外。理解我们可以从代码中期待什么是至关重要的，而不是像在海滩上寻找松露的猪一样挖掘回购协议。创建一个可靠的应用程序版本管理方法对于平息风波至关重要。

我们的新方法多年来运行良好，帮助我们开发了更好的 git 分支策略、发布说明管理和健壮的部署管道。

顺便说一下，你可以在我的帖子[这里](https://levelup.gitconnected.com/better-git-branching-strategy-multi-apps-monorepos-and-multiple-teams-in-focus-cd17b56962f2)中阅读**SimGit 流**分支策略。

# 什么是 SemVer 2.0？

一组简单的规则和要求解释了如何分配和增加版本号。这些规则基于但不一定限于封闭和开放源码软件中已存在的广泛标准实践。它们被称为“语义版本化”在这种模式下，版本号及其变化方式传达了底层代码的含义，以及从一个版本到下一个版本修改了什么。

考虑 X.Y.Z ( `Major.Minor.Patch`)的版本格式。不影响 API 的错误修复会增加补丁版本，向后兼容的添加/更改会增加次要版本，向后不兼容的更改会增加主要版本。

在此阅读更多关于语义版本的信息:

 [## 语义版本 2.0.0

### 给定主要版本号。补丁，增加:主要版本当你做不兼容的 API 改变，次要…

semver.org](https://semver.org/) 

它在设计时考虑了 API，但与前端应用程序配合得非常好，特别是在一个 mono repo 中处理多个应用程序并在它们之间共享一系列功能(核心)时。 **SimGit 流**分支策略利用这种版本化进行分支命名；因此，如果您使用 SemVer 方法，这是一个完美的匹配，因为您可以仅基于版本号来信任向后兼容性。

# 这是怎么回事？

给定一个版本号`MAJOR.MINOR.PATCH`，增加:

1.  **进行不兼容更改时的主要版本。这将包括重大的重新设计和我称之为“开启一个新时代”的东西。例如，在 Yell 的 4 年里，我们只开发了 9.x.x 版本。**
2.  **添加向后功能时的次要版本。**
    这些将包括站点中添加了新特性和功能的典型发布。有时，这些更新可能无法从你的 mono repo 向后兼容给定的应用程序，但这没关系，只要核心(共享组件)是兼容的。在某些情况下，我们可能会使用小程序来修复 bug，但这取决于情况，因为我们并不总是希望使用热修复方法。
3.  **做向后兼容的 bug 修复时的补丁版本。**
    当一个新的构建准备为 x.x.0 时，我们做测试，找 bug，修复，再部署。每次迭代和修复都会增加补丁版本。在 QA 验证之后，一个新版本几乎总是需要一些小的修正。当我们需要对实时代码进行修补时，几乎总是会用到补丁程序。

# 我们如何在应用中使用 SemVer？

*   我们将版本存储在`package.json` **文件**中。对于这样的信息来说，这是一个合理的选择，构建过程可以很容易地访问它。
*   我们使用版本号进行**缓存破坏**。我们所有的构建文件和资产都有应用版本的后缀，所以每当部署新代码时，我们都不必担心缓存问题。此外，人们可以快速识别问题的根源和部署的版本。
*   我们用`MAJOR.MINOR`来**命名我们的发布分支**。它有助于快速找到代码并推出特定版本的修补程序，而不会影响其他分支。
*   所有第三方分析和性能工具都会收到数据集中的版本号，因此我们可以快速识别异常和问题，并将其定位到特定版本。
*   在每个构建完成后，我们将所有文件压缩到一个包中，并使用**应用版本作为文件名**。只是让以后(再次)更容易找到东西。
*   我们希望创建基于版本的**发布环境，并在代码上线后立即销毁它们。这将允许我们在一个给定的时间有多个发布在进行中。**
*   …以及更多

所有的版本更新都由构建过程处理，当我们运行一个新的版本时，我们还定义了我们想要的 bump 类型——主要、次要或补丁。

**在主要或次要版本上—** 脚本创建一个新的发布分支来合并那里的所有代码。在此之后，对该特定版本的任何更新都是“补丁构建”。

**在补丁构建上—** 我们需要提供版本号(`major.minor`)，脚本将拉入这个特定的分支，修改补丁版本，构建一个包，并进行部署。

一旦一切都测试过了，我们很高兴，我们就可以推送 live 了。这都是我们自动化管道的一部分，它们将我前面提到的 SimGit 流分支策略、SemVer 版本控制和发布说明流缝合在一起(我将很快在另一篇文章中介绍)。

# 处理版本更新的示例脚本

这是我们构建过程以及我们如何处理它的一个例子。

您可能需要的几样东西:

```
/**
 * Install semver NPM package to deal with versioning
 */
npm install -D semver
npm install -D shelljs // optional
```

以及帮助您处理更新的脚本:

```
/**
 * Check what build you are preparing as you will need to pass
 * the name to semver script to update to the new version.
 */
const ALLOWED = {
    patch: true,
    minor: true,
    major: true
};if (!ALLOWED[process.argv.slice(2)[0]]) {
    console.log('\x1b[31m* Wrong version type!\x1b[0m');
    shell.exit();
} else {
    buildType = process.argv.slice(2)[0];
}/**
 * Run version update.
 * Read package.json, update version based on buildType, save back.
 */
let rawdata = fs.readFileSync('package.json');
let packageJson = JSON.parse(rawdata);
console.log('* Current version is', packageJson.version);
packageJson.version = semver.inc(packageJson.version, buildType);
console.log('* New version is', packageJson.version);

// save package json
let data = JSON.stringify(packageJson);
fs.writeFileSync('package.json', data);/**
 * Make it pretty again ;)
 */
shell.exec('npx prettier --write package.json');
```

好的一面是,`semver` npm 包会处理正确更新版本名所需的所有逻辑。你所要做的就是阅读当前版本，把它提供给`semver`,然后说你想用它做什么。

我们使用`**shelljs**`从 npm 脚本中执行 shell 函数。你会在上面的代码中看到这个`shell.exec()`。

![](img/1e191b4ef88e385744fce28fa82c29c8.png)

# 最后几句话…

这不是火箭科学，但我经常看到应用版本和工程团队在没有正式协议的情况下遵循松散或随机的方法。无论你决定做什么，确保它被写下来，被解释，并被所有开发人员执行。如果没有团队的遵循，最好的过程将会失败。

把`SemVer`看作是一个很好的起点，一个预警信号，一个代码的交通灯。它会给你的团队一个清晰的指示，什么时候版本变化是微小的，什么时候是严重的，并帮助你在部署过程中避免许多问题。一个主要版本是一件大事，通常需要同步多个团队，每个人都知道当他们准备升级时要小心。小调是常有的事，定期发布。补丁只是另一个迭代，没什么可担心的。

工作就像一种享受，很容易理解和遵循。