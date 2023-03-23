# 如何在 Python 中将处理多个文件的速度提高 10 倍

> 原文：<https://betterprogramming.pub/how-to-process-multiple-files-10x-faster-in-python-4f0ea9c7f64a>

## 不要循环，同时运行每个迭代

![](img/e03aa4fbe4ddb8559d432610b34267fe.png)

夏洛特·科内比尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

假设您有 100 个文件需要处理，或者您需要运行一个函数，其中您要修改输入参数数百次。如果处理一个文件或一个函数调用只需要几分之一秒，那么你可以运行一个简单的`for`循环。

然而，如果每次迭代需要五分钟，那么这很容易占用一整天的时间。如果您有幸在具有许多节点的高性能计算机(HPC)上工作，本文将解释如何一次处理多个文件并充分利用 HPC，这将节省时间并帮助您提高效率。

# 什么是 Slurm？

[Slurm](https://slurm.schedmd.com/documentation.html) 是许多多用户 HPC 上的作业调度器。它用于对作业进行排队，直到计算资源可用。本文将使用一个 bash 脚本，该脚本使用`[sbatch](https://slurm.schedmd.com/sbatch.html#lbAB)`向 HPC 上的调度程序启动多个作业。每个作业将使用不同的输入参数调用相同的 Python 函数。

有几种不同的方法可以达到这个目标。然而，为每个作业运行单独的任务是最灵活的方法，这也是我在这里介绍它的原因。

# Python 函数

第一步是编写一个适用于单个文件或迭代的 Python 函数。下面的脚本并没有做任何有用的事情，只是为了说明一个概念。它首先等待五秒钟，然后在进行下一次迭代之前将文件名写入日志文件。在现实世界中，这个脚本会更多地参与进来，做一些有用的事情。

注意在`__main__`函数中`process_single_file`的参数是`sys.argv[1]`。这告诉`submit_slurm_script.sh`，我们将提供一个输入参数，这将在下一节讨论。注意是从`1`开始而不是`0`开始。可以用`sys.argv[2]`等指定多个参数。

# Bash 脚本

下面的 bash 脚本名为`submit_slurm_script.sh`，用于将多个作业部署到集群。该脚本运行十个作业，每个作业都有不同的名称:`py_job_1`、`py_job_2`等。另外十个同名的作业也在队列中，并在第一个作业完成后开始。例如，一旦名为`py_job_1`的第一个作业完成，下一个同名的作业就会开始。

让我们浏览一下这个文件中的每一行:

*   `#!/usr/bin/env bash`是指定语言的 shebang。
*   `source activate base`激活`conda`环境。
*   `cd /burg/home/ljg2157/scripts`更改工作目录。
*   `script=”slurm_python_script.py”`是我们提交的脚本名称的变量。
*   `let jobname=1`是增加作业名称的变量。

剩余部分在变量`year`上循环。循环的每次迭代使用`sbatch`向具有不同`job_name`的集群提交不同的作业。注意附加到每个`job_name`末尾的`jobnumber`的用法。另外，请注意，每次`sbatch`提交后`jobname`都会增加`1`。

## s 批次选项

以下是`sbatch`命令中每个选项的作用:

*   `account`:使用的资源将记入该账户。
*   `ntasks`:告诉 Slurm 控制器我们最多运行 *n* 个任务。
*   `mem`:指定每个节点分配的内存。
*   `time`:设定每个作业的运行时间限制。这里最好稍微开明一点。
*   `dependency`:用于推迟作业的开始时间。`Singleton`设置一个依赖关系，即在具有相同作业名称和用户的任何先前启动的作业终止后，该作业可以开始执行。
*   `job-name`:这是您提交给集群的作业分配的名称。可以启动多个同名的作业。参见上面的`dependency`。
*   `wrap`:**`wrap`后的命令字符串在一个简单的 shell 脚本中启动。这就是我们启动 Python 脚本的方式。**

**这只是您可以传递给`sbatch`的可能选项的一个子集。完整的列表可以在 Slurm 上找到[。](https://slurm.schedmd.com/sbatch.html)**

# **运行 Bash 脚本**

**最后要做的是运行 bash 脚本，将所有作业提交到队列中:**

```
bash submit_slurm_script.bash
```

**检查作业的状态以确保它们正在排队或运行总是一个好主意。Slurm 使用`squeue`来检查作业的状态。**

# **最后的想法**

**希望这有助于您在访问 HPC 时加快 Python `for`循环的速度。在本例中，HPC 没有在单核上使用传统的`for`循环花费 100 秒，而是通过将任务分散到多个内核上，将时间缩短到仅 10 秒。如果每次迭代花费几分之一秒的时间，并且您的迭代次数相对较少，那么这种方法可能是多余的。但是，如果您有数百个文件，并且每个文件的处理需要几分钟，这可以节省您一整天的等待时间。**

**在我的工作中，我经常需要重新网格化空间数据集，这非常耗时。投入时间去理解这是如何工作的，为我节省了无数的时间，让我更有效率。在 HPC 上工作而不充分利用它就像买了一个电动螺丝刀然后手动使用一样。**

# **登录档**

**下面是`log.txt`的输出。注意，前十个文件的时间戳大致相同，并且 Slurm 脚本没有按照提交的顺序完成。您会注意到，五秒钟后，下一批十个脚本开始运行。**

**感谢您的阅读。我很乐意帮助您解决在 HPC 上部署多个作业时可能遇到的任何问题。**

***感谢阅读和支持媒体作者***

**[](https://lukegloege.medium.com/membership) [## 通过我的推荐链接加入 Medium—Luke Gloege 博士

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

lukegloege.medium.com](https://lukegloege.medium.com/membership)**