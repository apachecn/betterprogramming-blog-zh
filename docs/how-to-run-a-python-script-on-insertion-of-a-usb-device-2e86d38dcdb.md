# 如何在插入 USB 设备时运行 Python 脚本

> 原文：<https://betterprogramming.pub/how-to-run-a-python-script-on-insertion-of-a-usb-device-2e86d38dcdb>

## 在 Linux 中插入 USB 时触发数据传输或其他过程的指南

![](img/5f0a3f95eef5816886cda34ffd8a6672.png)

Tobias Lystad 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片。

在我们最新的 Raspberry Pi 传感器项目中，我们希望有一种无网络方法来自动获取数据。这个问题的解决方案是通过在插入“授权的”USB 存储设备时运行 Python 脚本来实现的。

在这篇文章中，我将解释如何实现这样的壮举。

# USB 规则

最简单的部分在于提供一组关于插入时做什么的规则。其中包括一个用于插入 USB 的脚本和一个用于移除 USB 的脚本:

```
ACTION=="**add**", SUBSYSTEM=="usb", PROGRAM="**<full_path_here>**/on_usb_in.sh"
ACTION=="**remove**", SUBSYSTEM=="usb", PROGRAM="**<full_path_here>**/on_usb_out.sh"
```

这两行进入`etc/udev/rules.d`目录。例如:

```
nano /etc/udev/rules.d/custom_usb.rules
```

# USB 断开时

正如[这篇文章](https://raspberrypi.stackexchange.com/questions/51836/how-to-run-a-script-when-a-device-is-connected-to-usb)所建议的，我们可以使用一个锁文件，只在设备插入时显示，而不运行代码的多个实例。如果我们打算同时插入多个 USB，这种方法有其缺点，但这很少会导致正常使用的问题。

因为我们的“connect”文件将创建锁，所以 disconnect 脚本需要删除它。因此，我们的`.../on_usb_out.sh`脚本的内容是:

```
#!/bin/sh LOCK=/tmp/lockfile_for_plug_usb/bin/
rm -f /tmp/lockfile_for_plug_usb
```

# 在 USB 连接上

在识别新的通用串行总线时，我们已经知道我们想要创建一个锁文件。一旦这样做了，我们就可以在合理的范围内运行任何我们想要的脚本(也就是说，它们需要是有限的并且没有中断的可能性)。

`.../on_usb_in.sh`脚本将当前日期存储在一个日志文件中，然后运行一个 Python 脚本来确定是否有数据存储设备。如果是，它会将数据传输给它:

# Python 脚本

理论上，Python 脚本的内容可以做任何事情。在我的例子中，我首先调用一个允许 USB 完全安装的延迟期:

```
import os, time, re
time.sleep(10) # 
```

## 提取驱动器信息

接下来，我通过使用`blkid`命令提取关于所有连接的 USB 驱动器的信息:

```
devices = os.popen('sudo blkid').readlines()
```

从这里，我可以将每个条目提取到包含所有相关信息的 dictionary 对象列表中。由于我对 Raspberry Pi SD 卡不感兴趣，所以我只选择位于`/dev/sd**`内并且具有`sd[a-z][1-9]`格式的项目(例如`sda1`):

## 查看选定的设备

现在我们有了一个 USBs 列表，我们可以通过迭代以下内容来打印它们的位置、唯一标识符和标签名称:

```
for u in usbs:
    print ('Device %(LABEL)s is located at $(loc)s with UUID of $(UUID)s'%u )
```

这种功能有助于根据 UUID 筛选出特定设备，并且只为我们预先批准的设备执行任务。

## 增加

最后，如果我们希望向 USB 设备传输数据，我们可能需要安装 USB 设备。这是通过使用系统调用来完成的，系统调用要求我们将驱动器安装在我们选择的位置(例如`/myusb`):

```
os.system('sudo mount $(loc)s /myusb'%u)
```

这使我们能够正常地向它传输文件和从它传输文件:

```
cp ./file2backup /myusb
```

## 卸载

完成后，我们需要在拔下设备之前卸载它。同样，这是通过使用:

```
os.system('sudo umount /myusb')
```

# 使脚本可执行

您可能需要做的最后一件事是使用`chmod a+x <filename>`使所有文件可执行，并可能重启系统以使更改生效。

# 结论

我们已经创建了一个在插入和移除 u 盘时执行的规则。其中，我们使用一个锁文件来停止多次执行，并运行一个 Python 脚本来识别插件设备。如果一个设备符合我们的标准，我们就可以安装它，并按照我们的意愿处理它，所有这些都是以自动化的方式进行的。

对于 Raspberry Pi 传感器，这使我们能够高效地卸载所有测量数据，而无需网络或移动信号。这种方法最大的优点(除了简单之外)是，对于恶劣的环境，只需使用一个防水连接端口就可以进行数据传输！