# 用 Python 生成安全密码和令牌的最佳实践

> 原文：<https://betterprogramming.pub/best-practices-for-generating-secure-passwords-and-tokens-in-python-ebb91d459267>

## 使用内置模块生成加密保护的密码和令牌

![](img/7fb4017e30dcd5d1a775048aa3ef5b54.png)

詹姆斯·萨顿的照片

在本文中，您将学习生成强随机密码和加密保护令牌的正确方法。拥有安全随机数使我们能够管理敏感信息，如密码和安全令牌。我们将使用从 Python 3.6 开始可用的 secrets 模块。[官方文件](https://docs.python.org/3/library/secrets.html)声明:

> “…应优先使用机密，而不是随机模块中的默认伪随机数生成器，随机模块是为建模和模拟而设计的，而不是为安全或加密而设计的。”

本文分为三个部分:

1.  基本用法
2.  例子
3.  结论

让我们开始吧。

# 1.基本用法

secrets 模块提供了一些内置函数，我们可以用它们来生成数字和令牌。不需要设置，但我们需要在使用之前导入模块。

```
import secrets
```

## 生成一个随机数

使用`randbelow`功能生成一个数字。它接受一个整数，生成的数字介于 0 和输入整数减 1 之间。输入的整数必须大于 0

```
secrets.randbelow(2) # generate either 0 or 1
secrets.randbelow(10) # generate a number from 0 to 9
secrets.randbelow(0) # error
secrets.randbelow(-10) # error
```

您还可以使用`randbits`函数来生成一个随机数。它接受一个表示位数的整数。输入的整数必须大于 0。

```
secrets.randbits(1) # generate either 0 or 1
secrets.randbits(2) # generate a number from 0 to 3
secrets.randbits(4) # generate a number from 0 to 15
secrets.randbits(8) # generate a number from 0 to 255
```

## 从列表中生成随机元素

该模块还为我们提供了从非空序列中选择随机元素的方法。让我们使用`choice`功能来试试吧

```
colour = ['red', 'blue', 'green', 'purple', 'yellow']
secrets.choice(colour)
```

## 生成随机字节串

`token_bytes`函数是生成字节的完美选择。您可以指定一个整数作为参数。如果你不指定任何东西，它将确定一个随机整数。

```
secrets.token_bytes(8) # generate 8 random bytes string
```

您应该会看到类似这样的随机字节字符串:

```
b'\x1bq\x8e\x83\x08\xb2g\x17'
```

## 生成一个十六进制的随机字符串

如果你想要一个十六进制的字符串，你可以使用`token_hex`函数。就像`token_bytes`函数一样，它接受一个整数，这个整数用来产生 n 个字节，每个字节稍后将被转换成两个十六进制数字。

```
secrets.token_hex(16) # generate 16 random hexadecimal string
```

这是一个输出示例:

```
cd7b7fb7e0c5c1fa17389050f884526e
```

## 生成 URL 安全的字符串

有时，您可能希望为您的 web 应用程序使用 Base64 编码的字符串。对于这种用例来说,`token_urlsafe`函数非常方便。

```
secrets.token_urlsafe(16)
```

我得到了以下结果:

```
S357dE8QSuE
```

# 2.例子

在这一节中，我将概述一些生成安全密码和令牌的最佳实践。请自行测试它们。

## 生成 10 个字符的字母数字密码

```
import string
import secretsalphabet = string.ascii_letters + string.digits
password = ''.join(secrets.choice(alphabet) for i in range(10))
print(password)
```

*   `ascii_letters` —包含从 A 到 Z 的小写和大写

## 生成一个包含标点符号的 10 个字符的十六进制密码

```
import string
import secretsalphabet = string.hexdigits + string.punctuation
password = ''.join(secrets.choice(alphabet) for i in range(10))
print(password)
```

## 生成 10 个字符的密码，至少包含一个小写字母、一个大写字母和一个数字

```
import string
import secretsalphabet = string.ascii_letters + string.digits
while True:
    password = ''.join(secrets.choice(alphabet) for i in range(10))
    if (any(c.islower() for c in password) and any(c.isupper() for c in password) and any(c.isdigit() for c in password)):
        breakprint(password)
```

*   `islower` —确定字符是否为小写
*   `isupper` —确定字符是否大写
*   `isdigit` —确定字符是否为数字

## 生成一个 10 个字符的密码，至少包含两个大写字母和两个数字

```
import string
import secretsalphabet = string.ascii_letters + string.digits
while True:
    password = ''.join(secrets.choice(alphabet) for i in range(10))
    if (sum(c.isupper() for c in password) >= 2 and sum(c.isdigit() for c in password) >= 2):
        breakprint(password)
```

## 生成唯一的四字密码

```
import secretsanimal = ['horse', 'elephant', 'monkey', 'donkey', 'goat', 'chicken', 'duck', 'mouse']
fruit = ['apple', 'banana', 'peach', 'orange', 'papaya', 'watermelon', 'durian']
electronic = ['computer', 'laptop', 'smartphone', 'battery', 'charger', 'cable']
vegetable = ['lettuce', 'spinach', 'celery', 'cabbage', 'turnip', 'cucumber', 'eggplant']
word_list = animal + fruit + electronic + vegetablepassword = set()
while True:
    password.add(secrets.choice(word_list))
    if(len(password) >= 4):
        breakprint(' '.join(password))
```

## 为密码重置生成一个带有安全令牌的临时 URL

```
import secretsurl = 'https://mywebsite/reset?key=' + secrets.token_urlsafe()
print(url)
```

# 3.结论

让我们回顾一下今天所学的内容。我们开始探索由`secrets`模块提供的基本功能。

然后，我们测试生成一些随机密码和字符串令牌或字节令牌的函数。

最后，我们尝试使用该模块，生成了几种不同类型的安全可靠的密码。

请注意，您不应该将您的密码存储在任何容易恢复的纯文本或加密文件中。应该使用不可逆的单向哈希函数对它们进行加盐和哈希处理。

感谢阅读，希望你喜欢这篇教程。下一篇文章再见。

# 参考

1.  [https://docs . python . org/3.6/library/secrets . html # module-secrets](https://docs.python.org/3.6/library/secrets.html#module-secrets)