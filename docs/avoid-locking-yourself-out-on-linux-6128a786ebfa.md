# 如何避免将自己锁在 Linux 之外

> 原文：<https://betterprogramming.pub/avoid-locking-yourself-out-on-linux-6128a786ebfa>

## 编写 bash 脚本来自动化用户帐户管理

![](img/a3b573cdb12d2aaebe5e4b80be27c2f1.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

> “我将自己锁定在虚拟机之外，无法访问它！”

这是有可能发生的。每个开发人员一生中至少会遇到一次这种情况。

让我们找到一种方法，避免因为被锁定而不得不重新开始设置，或者清除整个虚拟机。

使用虚拟化时，通常需要手动管理用户和帐户，但这不是很有效。让我们编写一个 bash 脚本来处理新用户的身份验证。

我们首先使用 hashbang `!#`将脚本初始化为一个文件，可以被我们机器上的 Bash 访问。

这个 hashbang 将使用一个运行程序`/bin/sh`或`/bin/bash`的指令来设置程序加载器，然后将`/path/to/script`作为第一个参数传递。

让我们在自己的脚本中使用以下内容。

```
#!/env/bin/bash
```

这将位于我们需要创建的文件`filename.sh`的顶部。

接下来，我们将编写一个小 bash 语句来处理我们的身份验证——就像 champ 一样！

它将只允许具有来自`Sudo Group`的许可的`Users`使用完全相同的访问级别来制作额外的`Users` 。这将赋予新用户`Sudo`权限。

```
# CHECK SUDO USER GROUP ASSOCIATION 
if ! [ $(id -u) = 0 ]; then
   echo "I am not root! You cannot give sudo power to a new user!"
   exit 1
else
 echo "You have granted Sudo powers to a New user"
 sudo adduser developer
 sudo usermod -aG sudo developer
 su - developer
fi
```

因此，我们现在允许当前用户添加一个名为`Developer`的新用户。

只有当这个 Bash 脚本直接在机器上运行时，我们才会看到这些影响。

*由于这个脚本是专门为拥有 Sudo 权限的用户编写的，除非你使用拥有 root 权限的* `*sudo sh ./filename.sh*` *，否则你将无法正确运行它。让我们用 Sudo 运行一下，试一试。*

将这个新用户添加到系统的引导加载程序后，系统会询问您几个问题来验证他们的权限:

```
Enter the new value, or press ENTER for the defaultFull Name []: Room Number []: Work Phone []:Home Phone []:Other []:
```

这些问题可以通过命令行提示符/终端输入，供您参考。

建议保存这些信息以供您自己记录，但也可以使用回车键继续跳过此步骤。

填写完这些值后，您会看到一个选项，让您选择是否将这些更改导入到系统的引导程序中。

```
Is the information correct? [Y/n] 
```

键入`Y`并点击`Enter`继续完成新用户帐户。

正如您在 bash 脚本中看到的，我们在这个新用户帐户上调用了`Usermod`。这是因为我们需要给新创建的帐户`Sudo`权限。如果您不希望用户拥有`Sudo`权限，您可以省略这个命令或者在脚本中修改这一行。

然后，脚本应该通过`*su*` *将新的用户帐户交给您的 bash 会话。*

SU 命令允许您将当前运行的用户切换到同一会话中的另一个用户。*注意:必须提供密码，以便快速切换到不同的用户。如果不输入密码，用户会话将返回到当前用户，并且不会切换。*

也就是说，您已经成功运行了一个可以为您创建用户的脚本。它还可以防止您将自己锁在外面，因为在没有启用 Sudo 权限的情况下，您无法成功地更改自己的用户权限或改变其他用户的状态。

编码快乐！