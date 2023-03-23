# Python 预提交钩子的 2 个用例来整理您的 Git 库

> 原文：<https://betterprogramming.pub/2-use-cases-of-python-pre-commit-hooks-to-tidy-up-your-git-repositories-8d86c9c4f06b>

## 更好地组织代码库的策略

![](img/f0ed2ce249ebed4a92bdfc8de5e9edab.png)

凯莉·西克玛在 [unsplash](https://unsplash.com/) 上的照片

组织良好的回购导致组织良好的代码和更好的开发人员体验。

在本文中，我将向您展示预提交的两个用例，以维护更好的存储库。

# 引入预提交？

预提交是一个基于 python 的工具，它允许您“挂钩”到您的 git 库，并在提交更改时触发一个简单的代码。

它由出色的 python 开发人员和 YouTuber Anthony Sottile 编写和维护。

通过预提交，开发人员可以利用 gits 分布式架构，在提交代码审查之前进行简短的代码检查。

这省去了许多重复的和适得其反的团队间的交流，并允许代码评审者关注实际的代码实现，而不是表面上的问题。

通过在运行 ci\cd 构建之前使用 git 提交和代码审查，我们可以节省交付时间和一些相关成本。

# 快速启动

从 git 提交开始，你只需要 3 个步骤:

1.  从 pip 安装预提交:

```
pip install pre-commit
```

2.在您的存储库中创建预提交配置文件(稍后介绍)

3.将提交配置安装到 git repo 中:

```
pre-commit install
```

从那时起，每次提交新代码时，钩子都将根据您的配置文件执行。

# 用例 1:干净的输出

让我们以`Jupyter notebooks.`为例

Jupyter 笔记本包含 JSON 格式的源代码及其输出材料。

```
pip install pandas plotly jupyter
```

如果您按原样提交笔记本，您将提交它的输出，有时它会占用太多空间并污染存储库。

因此，让我们创建一个钩子来在提交时清理存储库。

1.让我们启动我们的 git 回购:

```
git init
```

2.现在，让我们添加一个预提交配置文件:

3.安装挂钩:

```
pre-commit install>>>
pre-commit installed at .git\hooks\pre-commit
```

4.运行 git 状态

```
git status>>>
On branch mainNo commits yetUntracked files:
  (use "git add <file>..." to include in what will be committed)
        .gitignore
        .pre-commit-config.yaml
        notebooks/
```

我已经把笔记本放在了`notebooks`目录下

5.跟踪所有文件

```
git add --all>>>
warning: LF will be replaced by CRLF in .gitignore.
The file will have its original line endings in your working directory
warning: LF will be replaced by CRLF in notebooks/notebook.ipynb.     
The file will have its original line endings in your working directory
```

6.提交更改

```
git commit -m "good commit">>>
jupyter-nb-clear-output..................................................Failed
- hook id: jupyter-nb-clear-output
- exit code: 1C:\workspace-vscode\pre-commit-demo\venv\Scripts\python.EXE: No module named nbconvert
```

我们的预提交失败了，因为它找不到`nbconvert`模块。
我们需要安装它:

```
pip install nbconvert
```

并再次提交:

```
git commit -m "good commit">>>
jupyter-nb-clear-output..................................................Failed
- hook id: jupyter-nb-clear-output
- files were modified by this hook[NbConvertApp] Converting notebook notebooks/notebook.ipynb to notebook
[NbConvertApp] Writing 878 bytes to notebooks\notebook.ipynb
```

现在，预提交通知我们文件已被修改。
它清除了笔记本的输出。
现在我们需要添加更改:

```
git add --all
```

并提交:

```
git commit -m "good commit">>>
jupyter-nb-clear-output..................................................Passed
[main(root-commit) 8560ba3] good commit
 4 files changed, 379 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 .pre-commit-config.yaml
 create mode 100644 notebooks/notebook.ipynb
 create mode 100644 requirements.txt
```

# 用例 2:静态代码分析

这可以分为两个领域:

## 林挺

让我们创建一个名为`scripts`的新目录，并添加一个名为`mainapp.py`的文件

在这个文件里，我们会写一些无用的代码:)

我已经在这里写了林挺[的好处](https://medium.com/better-programming/how-to-easily-extend-pylint-with-plugins-d8ead26a68ac)，现在是时候把林挺加入我们的工作流程了。

1.  运行 git 状态:

```
git status>>>
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        scripts
```

2.向预提交配置文件添加新的挂钩:

我们在预提交配置中添加了一个 id 为`pylint`的新钩子。

3.安装新的吊钩配置:

```
pre-commit install>>>
pre-commit installed at .git\hooks\pre-commit 
```

4.跟踪更改:

```
git add --all
```

5.犯罪

```
git commit -m "commit #2">>>
jupyter-nb-clear-output..............................(no files to check)Skipped
pylint...................................................................Failed
- hook id: pylint
- exit code: 16PYLINTHOME is now 'C:\Users\user\AppData\Local\pylint\pylint\Cache' but obsolescent 'C:\Users\user\.pylint.d' is found; you can safely remove the latter
************* Module somescript
scripts\somescript.py:1:0: C0114: Missing module docstring (missing-module-docstring)-----------------------------------
Your code has been rated at 5.00/10 
```

由于 pylint 中缺少模块 docstring 错误，提交失败。
现在让我们添加一个模块 docstring。

添加更改

```
git add --all
```

并提交:

```
git commit -m "commit #2"
jupyter-nb-clear-output..............................(no files to check)Skipped
pylint...................................................................Passed
[master b2d3105] commit #2
 2 files changed, 8 insertions(+), 2 deletions(-)
```

提交已通过:)

**注意—**Jupyter 笔记本的挂钩被跳过，因为没有对笔记本进行任何更改。这使得预提交非常高效。

## 格式化

就像林挺一样，我们可以触发自动格式化软件包，比如黑色的[和黑色的](https://github.com/psf/black)

我们在原始脚本中添加了函数`function`，但是 foo 的格式很难看。

1.  添加挂钩配置:

2.安装新挂钩

```
pre-commit install>>>
pre-commit installed at .git\hooks\pre-commit
```

3.跟踪更改:

```
git add --all 
```

4.犯罪

```
git commit -m "add function">>>
jupyter-nb-clear-output..............................(no files to check)Skipped
pylint...................................................................Passed
black....................................................................Failed
- hook id: black
- exit code: 1Executable `black` not found
```

我们的提交失败，因为找不到 black。
让我们安装它:

```
pip install black
```

并再次提交:

```
git commit -m "add function">>>
jupyter-nb-clear-output..............................(no files to check)Skipped
pylint...................................................................Passed
black....................................................................Failed
- hook id: black
- files were modified by this hookreformatted scripts\somescript.pyAll done! \u2728 \U0001f370 \u2728
1 file reformatted.
```

文件已经格式化！

好多了！

现在让我们添加更改:

```
git add --all
```

并提交:

```
git commit -m "add function"
jupyter-nb-clear-output..............................(no files to check)Skipped
pylint...................................................................Passed
black....................................................................Passed
[master 4bd3217] add function
 2 files changed, 12 insertions(+)
```

# 结论

预提交可以帮助您快速识别代码的本地克隆上的问题，而不需要一直进行 ci/cd 构建或代码审查。

它允许您在整个组织中有效地实施编码标准和约定，并拥有组织得更好的代码库。