# 不再有失败的构建！

> 原文：<https://betterprogramming.pub/no-more-failing-builds-8ac07ac3572c>

## 用 Git 钩子阻止你的配置项上的红色构建

![](img/67a9f44e34608f72ca230699744a9382.png)

来源:jenkins.io

你曾经没有在本地运行单元测试的情况下进行提交吗？大概，答案是肯定的，你的 CI 变红了。

每个人都会遇到这种情况——几乎不可能每次都记得手动操作。

谢天谢地，GIT 有一个出色的特性叫做[钩子](https://git-scm.com/book/uz/v2/Customizing-Git-Git-Hooks)。有了钩子，我们可以在资源库中的某些动作之前或之后触发脚本，检查我们的代码，使用一些 API，记录日志，或者任何我们想要的东西。

我们希望使用定制的 Git 挂钩，如果我们的单元测试失败，它将拒绝新的提交。我们将在每次提交之前运行单元测试，并检查结果是否为正。单元测试应该是轻量级的和快速的，所以它们不应该过多地影响我们的工作流程。这种方法在集成或 e2e 测试中会有更多的问题。我们不建议在每次提交之前自动运行这些。

我们首先在中创建`hooks`目录和`pre-commit`脚本。项目根目录下的 git 文件夹。这是 GIT 在本地使用的目录。

```
mkdir .git/hooks
touch .git/hooks/pre-commit
```

这非常简单，只触发根目录下的脚本`pre-commit.sh`脚本。

```
#!/usr/bin/env bash./pre-commit.sh
```

现在让我们创建一个`run_tests.sh`脚本来运行单元测试。根据我们使用的工具，这个文件看起来会有所不同。在 Python 中，该文件可能如下所示:

```
# Pytest
*py.test* test# unittest
python -m unittest
```

如果我们使用 PHP，根据我们选择的工具，它可能是:

```
# PHPUnit
phpunit test*# Codeception
php* vendor/bin/codecept run unit
```

`pre-commit.sh`是整个运作的大脑:

```
**#!/usr/bin/env bash** *./run_tests.sh* 
RESULT=$?

**if [[ $**{RESULT} **-ne** 0 **]]
then** RED='\033[0;31m'
    *printf* "**$**{RED}Unit tests failed. Commit rejected"
**fi** *exit* **$**{RESULT}
```

在 bash 中`=$?`给出了最后执行的脚本的状态代码。然后我们检查结果是否不是 0。退出代码 0 表示没有错误—所有测试都通过了。如果单元测试失败，我们用红色打印一条适当的消息，使它看起来更严重。

这是非常少的工作，并给了我们一个很好的功能，可以真正有助于日常开发。完整源代码可从以下网址获得:

 [## jkapuscik 2/提交前测试

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/jkapuscik2/pre-commit-test)