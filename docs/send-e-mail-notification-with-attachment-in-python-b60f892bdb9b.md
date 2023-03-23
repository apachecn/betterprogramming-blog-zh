# 如何用 Python 发送带附件的电子邮件通知

> 原文：<https://betterprogramming.pub/send-e-mail-notification-with-attachment-in-python-b60f892bdb9b>

## 自动生成有用的电子邮件，让主要利益相关方了解情况

![](img/43a161556556014fbe724e87c22a2d53.png)

[Artem Sapegin](https://unsplash.com/@sapegin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照。

借助 Python 自动化的强大功能来减少手动工作，每当我们完成一项任务时，生成一份报告并通过电子邮件发送给人们总是一个好方法，其中包括流程结果、描述，可能还有一些图表作为附件。

在任何拥有多个流程的组织中，如果流程没有运行或失败，除了通知之外，还会生成报告并发送给多个团队。基本上，对于我们自动化的每一种情况，在流程完成后发送电子邮件通知总是好的。

# 首先:导入所需的模块

我们将使用下面的模块生成一个 HTML 版本的邮件正文—包括一个图像—附加一个报告文件，并将电子邮件发送给个人或分发列表。

```
import smtplib
from email.message import EmailMessage
from email.utils import make_msgid
import mimetypes
```

# 为邮件中的电子邮件正文创建一个简单的 HTML 文件

现在，让我们创建一个简单的 HTML 文件(`mail_template.html`)，我们将在电子邮件中使用它作为模板来包含图像和文本以进行美化。我将在下面解释模板，并确定模板中使用的变量。

# 发送带附件的电子邮件的最终代码

下面是最终代码，它将读取模板文件以创建一个漂亮的邮件正文，嵌入一个图像作为邮件头，并在正文中包含一个动态文本，该文本可以作为函数参数传递并附加文件。

我将尝试用单独的块来解释下面的各个步骤。

现在，我们将查看上述函数的每个部分，并在生成电子邮件时查看它们的含义。

# 程序中使用的文件

```
mail_template_file = os.path.join(path_dir, 'mail_template.html')mail_image_file = os.path.join(path_dir, 'mail_header_image.jpg')rep_image_file = os.path.join(path_dir, 'report.pdf')
```

我使用了 OS 模块函数，这样无论程序是在 Windows 还是 Linux 下运行，都可以使用这种方法。

`mail_template.html` 会有一个基本的 HTML 版本的邮件模板。我使用了一个表来实现更好的行级对齐。

*   第一行中使用的`image_cid`变量将作为标题图像`mail_header_image.jpg`的占位符。
*   第二行中的`email_body`变量将用于电子邮件正文。

`report.pdf` 是将要附加到邮件中的文件。

# 用于发送电子邮件的地址

这里没什么好解释的。我准备了电子邮件要发送给谁的 id，并创建了一个列表，以便它可以用于发送给多个人或团队。

# 阅读 HTML 模板文件

读取 HTML 模板文件，并将邮件正文和图片嵌入到邮件中。

用图像设置替代 HTML 正文:

```
temp_file = open(mail_template_file, “r”)if temp_file.mode == ‘r’:contents = temp_file.read()
```

使用 HTML 模板中的变量映射将我们收到的文本作为参数嵌入电子邮件正文:

```
filemsg.add_alternative(contents.format(image_cid=image_cid[1:-1], email_body=email_body), subtype=’html’)
```

# 将图像作为标题嵌入电子邮件正文

阅读我们希望嵌入到电子邮件中的图像文件，只是为了看起来更好，并引起读者的注意。这里，我们将获得图像的类型，这将在消息参数中使用。同样，这将被直接放置在 HTML 模板的`image_cid`变量中。

现在将图片作为标题嵌入到邮件正文的顶部，用`open(mail_image_file, ‘rb’)`作为`img`。

了解图像的内容类型:

```
maintype, subtype = mimetypes.guess_type(img.name)[0].split(‘/’)
```

使用 HTML 模板文件中的变量映射嵌入图像:

```
msg.get_payload()[1].add_related(img.read(), maintype=maintype, subtype=subtype, cid=image_cid)
```

# 在电子邮件中附加一份 PDF 报告

在将图像嵌入到电子邮件中之后，让我们也包含一个附件，以便可以基于单独的用例来涵盖这两种情况。

这里，我们打开一个 PDF 文档，并将其作为附件添加到邮件中。这个过程再次涉及到识别文件的类型，但是不同的是我们使用`add_related`嵌入邮件，而我们现在使用`add_attachment`给消息附加一些东西。

现在将报告作为附件添加到带有`open(rep_image_file, ‘rb’)`和`img2`的电子邮件中。

了解图像的内容类型:

```
maintype, subtype = mimetypes.guess_type(img2.name)[0].split(‘/’)
```

将报告文件附加到电子邮件中:

```
msg.add_attachment( img2.read(), maintype=maintype, subtype=subtype, filename=”Report.pdf”)
```

# 发送电子邮件

最后，使用 SMTP 服务器发送电子邮件并退出该过程:

```
smtp_email = smtplib.SMTP('css-smtp.emaildomain.com')smtp_email.sendmail(from_id, to_addrs, msg.as_string())smtp_email.quit()
```

# 结论

这是一个非常简单的过程，我们将图像或文件嵌入/附加到电子邮件中，并使用 SMTP 服务器将其发送给个人/团队。修改邮件服务器域信息后，可以直接使用函数，使用`Subject`和`email body`作为参数进行调用。

```
send_report_mail( "Story completed", "Medium Story is completed and email notification is being sent" )
```