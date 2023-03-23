# 使用简单的 Ruby 脚本将数据库表导出为 CSV 格式

> 原文：<https://betterprogramming.pub/export-a-database-table-to-csv-using-a-simple-ruby-script-5577a0914eb0>

## 快速简单的指南

![](img/b235692fd75e6dbd6d25913f46729a7e.png)

由 [Taskin Ashiq](https://unsplash.com/@taskinhoo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

如果您有一个 Rails 项目，并且想将一个表格导出为 CSV 格式，而不必大费周章地找到一个 gem，安装并使用它，然后在不再需要时卸载它，我有一些好消息。这里有一个简单快捷的方法可以将数据库中的特定表格导出为 CSV 文件。

这是您需要运行的[代码](https://gist.github.com/foxumon/fdb30349545944eee58c7858e6bab23c)。您可以将它作为一个 rake 任务来运行，或者以另一种方式运行。

正如您所看到的，首先我们导入`CSV`——我们需要它用数据库中的数据写 CSV 文件。然后，我们选择想要导出到的文件的位置和名称，在我们的例子中，这个文件是包含在存储库 *public* 下的一个名为 *data.csv* 的文件。

然后，我们设置想要导出的表，并开始写入。我们还可以更改想要导出的属性——我们不必将它们都包含在数据库中。

就是这样！就是这么简单，却非常有帮助。