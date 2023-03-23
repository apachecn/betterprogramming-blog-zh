# 如何用 Python 创建文件完整性监视器

> 原文：<https://betterprogramming.pub/how-to-create-a-file-integrity-monitor-with-python-3e4bba66f4cf>

## 使用 Python 保护文件安全

![](img/e85bb81b79f9f0df8aa272f3a7cf31c3.png)

资料来源:Undraw

越来越多的人开始意识到自己设备中的网络安全风险。我看到人们保护其设备的一种常见方式是安装文件完整性监视器来保护敏感文件的安全，但当您可以在不到 10 分钟内轻松创建一个文件完整性监视器时，为什么还要购买它呢？

对于有抱负的渗透测试人员来说，这也是一个学习编程和散列的好项目。

# **那么它是如何工作的呢？**

每个状态下的每个文件都有自己唯一的散列，当文件被篡改时，散列会改变。因此，如果我们可以获取敏感文件的哈希，我们可以将其与基线进行比较，如果它发生变化，我们将知道文件中的内容也发生了变化。

# **为什么重要？**

篡改文件是提升系统权限(cron 作业/计划任务)的最佳技术之一，还会带来更多安全风险。文件完整性监视器可以帮助阻止这种情况。

尽管在大多数情况下，设置适当的文件权限、计划注销和良好的密码策略可以解决此问题，但文件完整性监控不会造成伤害，是一个很好的备份/最后一道防线。

```
import hashlib
import smtplib
from email.message import EmailMessage

print("Hello to File Integrity Monitor, please enter the file path of the thing you want to monitor")
filePath = input("Enter file path(ex. /home/downloads/passwords): ")
usrEmail = input("Enter your email")
usrPasswd = input("Enter your password that you set up in your email app password")
print("Make sure two factor authentication is on")def getHash(filePath):
    md5 = hashlib.md5()
    with open(filePath,'rb') as file:
        hash = file.read()
        md5.update(hash)
        return md5.hexdigest()
def sendEmail():
    message = EmailMessage()
    message.set_content(" ")
    message['subject'] = "Someone edited one of the files on the computer"
    message['from'] = usrEmail
    message['to'] = usrEmailserver = smtplib.SMTP("smtp.gmail.com", 587)
    server.starttls()
    server.login(usrEmail, usrPasswd)
    server.send_message(message)
    server.quit()baseline = getHash(filePath)
print("[+] Just calculated your baseline")
print("[+] Checking")while True:
    check = getHash(filePath)
    if check != baseline:
        sendEmail()
        print("[+] Someone edited the file")
        baseline = check
```

代码的分解

1.  导入库

```
import hashlib
```

2.获取文件位置(为了简洁，我们使用了一个输入字段)

```
filePath = input(“Enter file path(ex. /home/downloads/passwords): “)
```

现在我们必须创建一个函数，这样我们就可以很容易地获得/生成一个文件的散列。

3.创建一个函数来散列一个文件

```
def getHash(filePath):
```

4.声明散列类型(在本教程中我们使用了`md5`，但是任何类型都可以)

```
md5 = hashlib.md5()
```

5.以读取和二进制模式打开文件:

```
with open(filePath,’rb’) as file:
```

6.读取文件并将其放入变量中:

```
hash = file.read()
```

7.散列文件:

```
md5.update(hash)
```

8.以十六进制形式返回文件:

```
return md5.hexdigest()
```

因为我们现在有了一个工作函数，来检索文件的散列。我们必须创建一个基线，这样我们就可以将文件的哈希与原始文件进行比较。这将确保它没有被篡改。

9.将基线设置为哈希:

```
baseline = getHash(filePath)
```

我们现在有了一个比较哈希的基线，我们应该开始在一个循环中检查文件的哈希，所以如果有人编辑它，我们会知道。

10.创建一个永不停止的循环:

```
while True:
```

11.现在我们必须检查文件的哈希:

```
check = getHash(filePath)
```

12.并进行比较，看它是否发生了变化:

```
if check != baseline:
```

13.我建议，当它改变时，它应该警告我们:

```
print(“[+] Someone edited the file”)
```

14.确保它更新基线，否则它会不断警告我们:

```
baseline = check
```

我们希望它在后台运行，而不希望一直看着终端。所以我建议，电子邮件通知，你可以插入下面的代码。

```
from email.message import EmailMessage def sendEmail(): message = EmailMessage() message.set_content(" ") message['subject'] = "Someone edited one of the files on the            computer" message['from'] = usrEmail message['to'] = usrEmail server = smtplib.SMTP("smtp.gmail.com", 587) server.starttls() server.login(usrEmail, usrPasswd) server.send_message(message) server.quit()
```

我们现在只需要调用函数`sendEmail()`，它就会给我们发送一封电子邮件。

现在，我们只需添加一些输入字段和打印语句，使其更加用户友好和易于访问。

```
print(“Hello to File Integrity Monitor, please enter the file path of the thing you want to monitor”)filePath = input(“Enter file path(ex. /home/downloads/passwords): “)usrEmail = input(“Enter your email”)usrPasswd = input(“Enter your password that you set up in your email app password”)print(“Make sure two factor authentication is on”)
```

恭喜你，你已经做了一个文件完整性监视器。

您可以做很多很酷的事情来升级这个文件完整性监控-如关闭不必要的行为-可能性是无限的。