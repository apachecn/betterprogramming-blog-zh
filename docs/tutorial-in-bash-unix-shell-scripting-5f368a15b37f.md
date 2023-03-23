# Bash (Unix Shell)脚本教程

> 原文：<https://betterprogramming.pub/tutorial-in-bash-unix-shell-scripting-5f368a15b37f>

## 面向数据科学家和工程师的 Bash 脚本完整教程

![](img/a47f849d21eb9ae124823fdb5e80fca1.png)

图片由[预测黑客](https://predictivehacks.com/)

[](https://jorgepit-14189.medium.com/membership) [## 用我的推荐链接加入媒体-乔治皮皮斯

### 阅读乔治·皮皮斯(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

jorgepit-14189.medium.com](https://jorgepit-14189.medium.com/membership) 

Bash 是一种简洁、快速、健壮的数据和文件操作脚本语言。这是在云中构建分析管道的一项重要技能，Linux 用户喜欢使用存储在多个文件中的数据。在本文中，我们提供了一些实例，说明如何创建自己的 Bash 函数来自动化数据科学家的工作。

本教程完全基于 [DataCamp](https://learn.datacamp.com/courses/introduction-to-bash-scripting) 课程，我发现它对每个数据科学家都非常有用和重要。

对于本教程，查看一些基本的 [Unix 命令](https://predictivehacks.com/unix-commands-cheatsheet/)和一些[基本 Unix 示例](https://predictivehacks.com/basic-unix-commands-for-data-analysts/)以及一个 [cron 作业示例](https://predictivehacks.com/how-to-schedule-a-cron-job-in-linux/)可能会有所帮助。

# “Hello World”Bash 脚本示例

通常，bash 脚本以一个 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) `#!/usr/bash`开始，指示使用 Bash shell 执行文件。假设我们想要运行一个 Bash 脚本，打印“Hello World”和“Good Night World”。

```
#!/usr/bash 
echo "Hello World" 
echo "Good Night World"
```

脚本文件可以保存为`myscript.sh`，其中文件扩展名`.sh`表示这是一个 Bash 脚本。从技术上讲，如果第一行有 Bash 的 shebang 和 path，就不需要这样做。它可以在终端中使用`bash myscript.sh`运行，或者，如果您提到了第一行`!/usr/bash`，您可以简单地输入`./scipt_name.sh`。回到我们的例子，在终端中，我们键入:

```
bash myscript.sh
```

我们得到了:

```
Hello World 
Good Night World
```

让我们再举一个例子，这个例子使用了管道命令。假设我们有一个名为`employees.csv`的文件，其中包含雇员及其部门的用户 ID，如下所示(前五行):

```
UserID,Department
1234,Data_Science
1235,Project_Management 
1236,Data_Sicence
1237,Human_Resources
```

我们想创建一个 Bash 脚本，它按部门返回雇员人数。让我们称我们的脚本为`group.sh`。具体如下:

```
#!/usr/bash
cat employees.csv | cut -d "," -f 2| tail -n +2 | sort | uniq -c | sort -nrbash group.sh
```

输出:

```
10 Data_Science
5 Project_Management
3 Human_Resources
```

# 标准输入-标准输出-标准错误

在 Bash 脚本中，有三个*流*:

*   STDIN(标准输入)—进入程序的数据流
*   STOUT(标准输出)—程序输出的数据流。它的退出代码值为 0 表示成功，为 1 表示失败。
*   STDERR(标准错误)—程序中的错误。它的值为 2。

## 如何在脚本中使用退出代码

# 争论

Bash 脚本接受在执行脚本调用时指定的参数。`ARGV`是描述脚本中所有参数的术语。每个参数都可以通过`$`符号来访问——第一个是`$1`，第二个是`$2`，依此类推。最后，`[[email protected]](https://predictivehacks.com/cdn-cgi/l/email-protection)`和`$*`给出了`ARGV`中的所有参数，而`$#`给出了参数的长度(即数量)。

举例。让我们考虑一下`ex.sh`:

让我们跑吧:

```
bash args.sh one two three four five
```

我们得到:

```
one
two
one two three four five
There are 5 arguments
```

# Bash 中的基本变量

与其他语言类似，您可以用等号符号给变量赋值，如下所示:

```
# do not use space between equal sign
firstanme='George'
lastname='Pipis'
# you can reference them with the $
echo "Hello" $firstname $lastname
```

在 Bash 中，在创建变量和打印时，使用不同的引号可能意味着不同的事情。

*   单引号:Shell 解释字面意思
*   双引号:Shell 按字面解释，除了使用`$`和反斜线`“`。换句话说，双引号将美元符号后面的内容理解为变量。
*   反斜线:在壳中创建壳。因此，shell 运行命令并将 STDOUT 捕获回一个变量。命令`date`就是一个很好的例子。例如，您可以键入:

```
datenow='the date now is `date`'echo datenow
```

输出:

```
The date is Sat Apr 4 20:16:43 DST 2020
```

另一种方法是在 shell 中调用 shell，在我们的例子中，`date`是通过键入:

```
datenow="the date now is $(date)"echo datenow
```

# Bash 中的数字变量

我们可以用以下方法将两个整数相加:

```
expr 4 + 6
echo $((4+6))
# only the bc method allows operations with decimal numbers
echo "4+6" | bc
```

示例:构建一个 Bash 脚本，该脚本将华氏温度作为参数，并使用以下公式将其转换为摄氏温度:

![](img/cecf24e9638d8dd6e27380a8d6116d5c.png)

让我们调用我们的脚本`script.sh`并获得 100 华氏度的摄氏度数。

调用脚本:

```
bash script.sh 100
```

我们得到的输出是:

```
37.77
```

# Bash 中的数组

Bash 中有两种类型的数组:

**数组**

*   正常数字索引结构
*   在 Python 中称为*列表*或者在 R 中称为*向量*

**关联数组**

*   类似于普通数组，但有键值对，而不是数字索引
*   类似于 Python 的*字典*或者 R 的*列表*
*   注意:从 Bash 4 开始，这是可用的

有两种方法可以创建数组:

```
# declare without adding elements
declare -a my_array# create and add elements at the same time
my_array=(1 2 3)
```

记住，等号两边不能有空格，元素之间不能有逗号！

可以使用`array[@]`返回所有数组元素。请注意，当您想要访问这些属性时，Bash 需要在数组名两边加上花括号。您可以使用方括号访问数组元素。注意，Bash 对数组使用零索引。您可以使用`array+=(elements)`添加数组。示例:

输出

```
1 2 3 10
4
10
5
2 3 10
5 2 3 10 20
```

关联阵列的示例:

```
# Create empty associative array
declare -A city_details
city_details=([city_name]="New York" [population]=10000000)echo ${city_details[city_name]} #index using key to return a value# return all the keys
echo ${!city_details[@]}
```

输出:

```
New York city_name population
```

# 如果语句

Bash 中的基本 IF 语句具有以下结构:

```
if [ condition ]; then
    # some code
else
    # some code the else statment is optional
fi
```

注意，方括号和条件元素之间有空格，在右括号`];`后面有一个分号。

*   `=`相等
*   `!=`不平等
*   `>`更大
*   `<`比较少
*   `-eq`相等
*   `-ne`不等于
*   `-lt`小于
*   `-le`小于或等于
*   `-gt`大于
*   `-ge`大于或等于
*   `-e`如果文件存在
*   `-s`如果文件存在且大小大于零
*   `-r`如果文件存在且可读
*   `-w`如果文件存在且可写
*   `$$`对于和
*   `||`为或

更多的条件标志，可以看看 [gnu Bash 手册。](https://www.gnu.org/software/bash/manual/html_node/Bash-Conditional-Expressions.html)

## 锻炼

假设有一个名为`employees/`的文件夹，其中包含表单中每个员工的文本文件:

```
Name: George Pipis
Hiring Date: 2010
Salary: 30000
Department: Data Science
```

您希望编写一个脚本，将雇员的相应文本作为输入，并根据雇用日期将其移动到一个新文件夹中。如果聘用日期大于或等于 2018 年，那么它将进入`new_employee/`文件夹；否则，到`old_employee/`文件夹。

运行`George_Pipis.txt`的脚本

## 解决办法

`script.sh`

我们运行它:

```
bash script.sh employees/George_Pipis.txt
```

# Bash 中的 FOR 循环

Bash 的基本结构是:

```
for x in 1 2 3
do
    echo $x
done
```

输出:

```
1
3
5
```

也可以用`brace expansion`，也就是`{START..STOP..INCREMENT}`。

```
for x in {1..5..2}
do
    echo $x
done
```

还有`three expression syntax`:

```
for ((x=2;x<=4;x+=2))
do
    echo $x
done
```

输出:

```
2
4
```

## 全球扩张

```
for employee in employees/*
do
    echo $employee
done
```

## 壳中壳到 FOR 循环

我们可以循环调用 shell-in-a-shell 的结果:

```
for employee in $(ls empoyees/ | grep -i 'georg')
do
    echo $employee
done
```

输出:

```
George_Pipis_txt
George_Papadopoulos.txt
John_Georgiades.txt
```

## 锻炼

你想写一个脚本，打印出文件夹`my_code/`中所有以`.py`结尾的文件。

## 解决办法

```
for file in my_code/*.py
do
    echo $file
done
```

## 锻炼

在一个名为`my_python_tests/`的文件夹中有许多 Python 脚本，其中一些是关于文本挖掘任务的。你可以假设所有包含代码`import re`的 Python 脚本都可以归类为`text mining`任务。您的任务是编写一个 Bash 脚本，将所有文本挖掘脚本移动到一个名为`text_mining/`的文件夹中。

## 解决办法

`script.sh`可以如下:

# WHILE 语句语法

迭代继续，直到这个条件不再成立。

*   用`while`这个词代替`for`。
*   用方括号将条件括起来。
*   对 IF 语句中的数字比较使用相同的标志。
*   多个条件可以链接，或者使用双括号，就像`IF`语句一样，加上`$$`、`||`。
*   确保您的循环终止！

```
x=1
while [ $x -le 3 ];
do
    echo $x
    ((x+=1))
done
```

输出:

```
1
2
3
```

# 构建案例陈述

CASE 语句的结构是:

让我们编写一个 Bash 脚本`script.sh`,它将一个字符串作为输入，并返回是工作日、周末还是非工作日。

输出:

```
It is a Weekday!
```

## 锻炼

文件夹`my_r_scripts`包含 R 机器学习模型的 R 脚本。我们的任务是将基于树的模型移动到名为`tree_models/`的文件中，并删除 KNN 和逻辑回归模型。

## 解决办法

我们将使用 CASE 语句。

# Bash 中的函数

Bash 函数的语法是:

```
function name () {
    # your code
    return #return something
    }
```

或者可选地

```
function function_nmae {
    # your code
    return #something
    }
```

示例:让我们编写一个函数，遍历文件夹中的 Python 文件并打印出它们的名称。

## 锻炼

在 Bash 脚本中编写一个返回当天的 Bash 函数。

## 解决办法

输出:

```
Sun
```

# 将参数传递给 Bash 函数

正如我们前面看到的，您可以使用`$1`、`$2`符号将参数传递给函数。此外，您还可以使用`[[email protected]](https://predictivehacks.com/cdn-cgi/l/email-protection)`、`$*`和`$#`。

## 例子

创建一个函数，将两个数字作为输入，并返回它们的比率。

## 例子

编写一个对数组求和的函数:

输出:

```
The sum of the test array is 84.84
```

感谢阅读！我希望这有所帮助。