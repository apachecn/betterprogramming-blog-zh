# R 语言中字符串和正则表达式的简明指南

> 原文：<https://betterprogramming.pub/string-manipulation-and-regular-expressions-in-r-6686c09920fe>

## 轻松驯服你的琴弦

![](img/3307ca07bd0873c9106e30bd8d454483.png)

照片由[格伦·凯莉](https://unsplash.com/@glencarrie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如今查找数据很容易。找到高质量的数据比以往任何时候都难。低质量数据的一个长期特征是混乱且保存不准确。不管我们数据专家多么喜欢谈论算法和模型验证，我们大部分时间都花在清理和整理数据上。

从这个意义上来说，处理字符串需要一套与`data.frame`或列表稍微不同的技能。您可能已经猜到了，本文的主题是:如何尽可能轻松地操纵和驯服字符串。我们开始吧！

# 粘贴和拆分

粘贴和拆分字符串是我们经常面临的两个最常见的任务。

对于这两个简单任务，我们有两个同样简单的函数:`paste()`和`strsplit()`。

```
paste('Ugurcan' , 'Demir' , sep = " ")## [1] "Ugurcan Demir"## [[1]]
## [1] "Ugurcan" "Demir"strsplit("Ugurcan Demir" , split = " ")paste("The","United" ,"States" ,"of" ,"America" , sep = " ")## [1] "The United States of America"unlist(strsplit("The United States of America" , split = " "))## [1] "The"     "United"  "States"  "of"      "America"
```

# 字符数和切片

r 和 Python 用户往往会有很多交集。这两种语言都很容易掌握，它们的通用库都面向统计学家、机器学习从业者或任何对科学计算感兴趣的人。但是如果你来自 Python，我们将要展示的例子可能会有点奇怪。

例如，要找到字符的总数，`length()`将是一个直观的选择。但与 Python 不同的是，这不是 r 中的工作方式。

```
length("The United States of America")## [1] 1nchar("The United States of America")## [1] 28
```

切片也不是常规切片。为此我们有两个函数:`substr()`和`substring()`。如果同时指定 start 和 stop 参数，这两个同卵双胞胎的工作方式完全相同。然而，`substring()`有一个默认的停止值，而`substr()`没有。

```
substr("The United States of America" , start = 10 , **stop** = 20)## [1] "d States of"substring("The United States of America" , first = 10 , last = 20)## [1] "d States of"
```

下面是当我们没有将参数传递给“stop”或“last”参数时会发生的情况。

```
substring("The United States of America" , first = 10 )## [1] "d States of America"substr("The United States of America" , start = 10 )## Error in substr("The United States of America", start = 10): argument "stop" is missing, with no default
```

# regexec()、gregexpr()和 grep()

我可以听到你大声说“我们首先应该如何知道作为参数传递的索引”。当你有一段字符串时，用手指数很容易，但是当你有几百万甚至几十行数据时，用手指数是非常不切实际的。幸运的是，我们配备了两个漂亮的功能来做到这一点。

我们的第一个函数`regexec()`用于在一个更大的字符串中查找第一个出现的子字符串。

```
regexec(pattern = 'United' , text =  "The United States of America"  )## [[1]]
## [1] 5
## attr(,"match.length")
## [1] 6
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUEregexec(pattern = 'U' , text =  "The United States of America"  )## [[1]]
## [1] 5
## attr(,"match.length")
## [1] 1
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
```

另一方面，`gregexpr()`在一个更大的字符串中查找一个子字符串的所有匹配项。

```
gregexpr(pattern = 'e' , text =  "The United States of America"  )## [[1]]
## [1]  3  9 16 24
## attr(,"match.length")
## [1] 1 1 1 1
## attr(,"index.type")
## [1] "chars"
## attr(,"useBytes")
## [1] TRUE
```

函数接受第二个参数，不仅仅是一个字符串，而是一个字符串向量，并返回包含子字符串的元素的索引。如果将 value 参数设置为 TRUE，它将返回元素本身。

```
grep(pattern = 'wigh' , x = c('Michael' , "Jim" , "Dwight" , "Pam") )## [1] 3grep(pattern = 'm' , x = c('Michael' , "Jim" , "Dwight" , "Pam") )## [1] 2 4grep(pattern = 'm' , x = c('Michael' , "Jim" , "Dwight" , "Pam") , value = T)## [1] "Jim" "Pam"
```

# sub()和 gsub()

`sub()`和`gsub()`将它带到下一个层次，用作为参数传递的另一个字符串替换一个更大的字符串中与给定子串匹配的部分。

```
sub(pattern = 'm' , replacement = "n" , x = c('Michael',"Jim","Dwight","Pam"))## [1] "Michael" "Jin"     "Dwight"  "Pan"sub(pattern = "i" , replacement = "a" , x = "The United States of America")## [1] "The Unated States of America"
```

如果你注意到“美国”这个词保持不变。这是因为 sub()只替换第一次出现的子字符串。为了替换它们，我们应用`gsub()`。

```
gsub(pattern = "i" , replacement = "a" , x = "The United States of America")## [1] "The Unated States of Ameraca"
```

# 正则表达式(REGEX)

到目前为止，我们一直在其他字符串中搜索简单的子字符串。要搜索的子字符串可能不像我们到目前为止在这些例子中展示的那样简单。我们甚至可能不知道我们需要找到什么特定的子串，而是需要找到任何符合我们给出的蓝图的东西。在这些情况下，我们利用正则表达式:或简称 REGEX。

正则表达式可以在许多编程语言中找到，只是实现略有不同。他们的主要工作是在给定的大字符串中搜索字符串的模式。

正则表达式没有给出要查找的确切子串，而是查找与给定子串相似的模式。他们有自己的小语种来做这项工作。我们将详细解释管理正则表达式的所有元字符和规则。

# 元字符

首先，我们应该声明正则表达式的迷你语言的符号被称为`metacharacters`，它们是正则表达式的主干。

*   “$”
*   “*”
*   “+”
*   “.”
*   “?”
*   “[ ]”
*   “^”
*   “{ }”
*   “|”
*   “( )”
*   “\ ”

现在我们将解释这些`metacharacters`的作用。

# 量词

"?"、“*”、“+”和“{ }”在`metacharacters`中被称为量词，因为它们表示我们希望看到给定模式多少次。

*   " * ":如果前面的项目出现 0 次或更多次，则匹配
*   "+":如果前面的项目出现 1 次或多次，则匹配
*   "?":匹配之前的项目出现 0 次或 1 次
*   " {，m} ":如果前面的项目出现 m 次或更少，则匹配
*   “{n，}”:如果前面的项目出现 n 次或更多次，则匹配
*   “{n，m}”:如果前面的项目出现 n 到 m 次，则匹配
*   “{m}”:如果前面的项目正好出现 m 次，则匹配

```
letter_vector <- c(
  "AACACA","BBCCBC","CCABBB","ABABAA","ACBCAA","BCACBC",
  "BABABA","CACABA","BBABAB","BCCBAB","CAABCC","BCCBCA",
  "CAAABA","BAABCB","CCABBC","ABABBA","CABAAC","CAABCC",
  "CABCAC","AABCAA","CAAACB","BBACCA","BCAAAB","BBACBC",
  "CCCCBC","ACABCA","BCBBBC","AABBCC","CCBBBB","BBABBA","BBCAAC"
)grep(pattern = "ABC" , x =  letter_vector , value = T)## [1] "CAABCC" "BAABCB" "CAABCC" "CABCAC" "AABCAA" "ACABCA"grep(pattern = "AB*C" , x =  letter_vector , value = T)##  [1] "AACACA" "ACBCAA" "BCACBC" "CACABA" "CAABCC" "BAABCB" "CCABBC" "CABAAC"
##  [9] "CAABCC" "CABCAC" "AABCAA" "CAAACB" "BBACCA" "BBACBC" "ACABCA" "AABBCC"
## [17] "BBCAAC"grep(pattern = "AB+C" , x =  letter_vector , value = T)## [1] "CAABCC" "BAABCB" "CCABBC" "CAABCC" "CABCAC" "AABCAA" "ACABCA" "AABBCC"grep(pattern = "AB?C" , x =  letter_vector , value = T)##  [1] "AACACA" "ACBCAA" "BCACBC" "CACABA" "CAABCC" "BAABCB" "CABAAC" "CAABCC"
##  [9] "CABCAC" "AABCAA" "CAAACB" "BBACCA" "BBACBC" "ACABCA" "BBCAAC"grep(pattern = "AB{,2}C" , x =  letter_vector , value = T)##  [1] "AACACA" "ACBCAA" "BCACBC" "CACABA" "CAABCC" "BAABCB" "CCABBC" "CABAAC"
##  [9] "CAABCC" "CABCAC" "AABCAA" "CAAACB" "BBACCA" "BBACBC" "ACABCA" "AABBCC"
## [17] "BBCAAC"grep(pattern = "AB{2,}C" , x =  letter_vector , value = T)## [1] "CCABBC" "AABBCC"grep(pattern = "AB{1,2}C" , x =  letter_vector , value = T)## [1] "CAABCC" "BAABCB" "CCABBC" "CAABCC" "CABCAC" "AABCAA" "ACABCA" "AABBCC"grep(pattern = "AB{2}C" , x =  letter_vector , value = T)## [1] "CCABBC" "AABBCC"
```

# 开始和结束元字符

“^”和“$”分别代表字符串的开头和结尾。他们在其他资源中也被称为锚。锚点不匹配任何字符。

```
grep(pattern = "^A" , x =  letter_vector , value = T)## [1] "AACACA" "ABABAA" "ACBCAA" "ABABBA" "AABCAA" "ACABCA" "AABBCC"grep(pattern = "C$" , x =  letter_vector , value = T)##  [1] "BBCCBC" "BCACBC" "CAABCC" "CCABBC" "CABAAC" "CAABCC" "CABCAC" "BBACBC"
##  [9] "CCCCBC" "BCBBBC" "AABBCC" "BBCAAC"
```

# 占位符

"."是我们的下一个元字符，它匹配任何使用它的字符。以下示例搜索以“C”开头、以“A”结尾以及除两个字符之外的任何字符的任何模式。

```
grep(pattern = "C..A" , x =  letter_vector , value = T)## [1] "AACACA" "ACBCAA" "CACABA" "BCCBAB" "BCCBCA" "CAAABA" "CABAAC" "CAAACB"
## [9] "BCAAAB"
```

# 顺序

元字符“\”，当它与一组序列关键字字母一起使用时，用于定义字符串中的某个字符序列，并且当它与我们的字符串函数一起使用时，它匹配那些字符序列。下面是经常与“\”一起使用的关键字母的详细列表。

*   " \d" =数字
*   " \D" =不是数字
*   " \w" =单词字符(a-z，A-Z，0？？？9)
*   " \W" =不是单词字符
*   " \s" =空白
*   " \S" =不是空白
*   " \b" =单词边界
*   " \B" =不是单词边界

让我们看一些例子。

```
string1 <- 'My name is Ugurcan and I am 25.'gsub(pattern = "\\d" , replacement = "-" , x = string1)## [1] "My name is Ugurcan and I am --."gsub(pattern = "\\s" , replacement = "-" , x = string1)## [1] "My-name-is-Ugurcan-and-I-am-25."gsub(pattern = "\\w" , replacement = "-" , x = string1)## [1] "-- ---- -- ------- --- - -- --."gsub(pattern = "\\b" , replacement = "-" , x = string1)## [1] "-M-y- -n-a-m-e- -i-s- -U-g-u-r-c-a-n- -a-n-d- -I- -a-m- -2-5-.-"
```

# 字符类别

“[ ]”是另一个元字符，它经常用于形成复杂的模式来分析复杂和非结构化的文本数据。我们可以在方括号内传递几个字符，但是它只匹配这些字符，并且只匹配一个字符。顺序无关紧要，我们可以使用连字符来搜索一系列字符或数字。

```
grep(pattern = "[zp]" , x = state.name , value = T)## [1] "Arizona"       "Mississippi"   "New Hampshire"grep(pattern = "[b-d]" , x = state.name , value = T)##  [1] "Alabama"       "Colorado"      "Connecticut"   "Florida"      
##  [5] "Idaho"         "Indiana"       "Kentucky"      "Maryland"     
##  [9] "Massachusetts" "Michigan"      "Nebraska"      "Nevada"       
## [13] "New Mexico"    "Rhode Island"  "Wisconsin"grep(pattern = "[od]$" , x = state.name , value = T)## [1] "Colorado"     "Idaho"        "Maryland"     "New Mexico"   "Ohio"        
## [6] "Rhode Island"
```

对于某些特定类型的场合，你也可以用方括号将类名内建起来。下面我们给出了这些类名的完整列表。

*   `[:alnum:]` =字母数字字符:[:字母:]和[:数字:]
*   `[:alpha:]` =字母字符:[:下:]和[:上:]
*   `[:blank:]` =空白字符:空格和制表符，可能还有其他与区域设置相关的字符，如不间断空格
*   `[:cntrl:]`= ASCII 中的控制字符，这些字符有八进制代码 000 到 037，在另一个字符集中有 177 (DEL)，这些是等效字符(如果有的话)
*   `[:digit:]` =位数:0 1 2 3 4 5 6 7 8 9
*   `[:graph:]` =图形字符:[:alnum:]和[:punct:]
*   `[:lower:]` =当前语言环境中的小写字母
*   `[:print:]` =可打印字符:[:alnum:]，[:punct:]和空格
*   `[:punct:]` =标点符号:！" # $ % & ' ( ) * +，—/:；< = >？@ [ ] ^ _ ` { | } ~."
*   `[:space:]` =空格字符:制表符、换行符、垂直制表符、换页符、回车符、空格和其他可能的语言环境相关字符
*   `[:upper:]` =当前语言环境中的大写字母
*   `[:xdigit:]` =十六进制数字:0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f

# 分组与或运算符

我们的最后两个元字符是“()”和“|”，它们通常一起使用。分组元字符“()”分隔不同的模式集，or 运算符的工作方式类似于常规 OR 运算符。让我们用例子来说明它们。

```
grep(pattern = "(th|la)" , x = state.name , value = T)##  [1] "Alabama"        "Alaska"         "Delaware"       "Maryland"      
##  [5] "North Carolina" "North Dakota"   "Oklahoma"       "Rhode Island"  
##  [9] "South Carolina" "South Dakota"grep(pattern = "^New (Y|J)" , x = state.name , value = T)## [1] "New Jersey" "New York"
```

# 转义元字符

我们已经看到了所有的元字符，但是如果我们的搜索模式包括这些元字符中的一个呢？

这是一个非常欢迎你问的问题。在这种情况下，我们需要告诉 R，我们不使用这些字符作为元字符，而是作为常规字符。

为了做到这一点，我们在元字符前面添加了一个反斜杠，这样我们就可以对它进行转义。因为反斜杠也是元字符，所以我们也添加了另一个反斜杠来转义它。这里有一些例子。

```
string2 <- c("Lionel Messi\ PSG" , 'file_name$' , "{2022}")grep(pattern = "\\$" , x = string2  , value = T)## [1] "file_name$"grep(pattern = "\\{" , x = string2  , value = T)## [1] "{2022}"
```

在本文中，我们首先讨论了字符串使用的所有函数。然后我们讨论了 11 个元字符，用它们我们可以创建复杂的模式。希望这篇文章能成为大家的参考指南。