# 构建您自己的家庭云存储(第 1 部分)

> 原文：<https://betterprogramming.pub/build-your-own-in-home-cloud-storage-1aa74b5c6397>

## 将可共享存储添加到您的家庭网络中

![](img/e991895eec38445c89600a18fa6ede0b.png)

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=196230) 的 [Zsolt Oravecz](https://pixabay.com/users/zsoravecz-31710/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=196230) 提供

有一台旧电脑在角落里积灰？将其转变为网络存储解决方案！我就有这样一台电脑，所以我的周末项目将是给它第二次生命，作为我的网络和我的家庭 Kubernetes 集群的共享存储(是的，我是一个真正的技术极客)。

在之前的一篇文章中，[玩 Kubernetes](https://medium.com/@rlkamradt/playing-with-kubernetes-bf83a98e7490) ，我用一台旧电脑玩了玩 Kubernetes。从那以后，我买了一个翻新的服务器机架来放我的 Kubernetes 沙盒，我的旧小电脑又开始积灰了。我为这个小家伙感到难过，尽管它只有 150 的硬盘，我想我应该给它一个目标。

接下来，您需要一台使用时间不超过 5 到 10 年的电脑、一个可以接入互联网的家庭网络、一台可以将 ISO 映像刻录到 DVD 的电脑，以及一张可以刻录映像的空白 DVD。如果你的旧电脑可以从 u 盘启动，你也可以使用至少有 1g 空间的 u 盘。我有一个外置 DVD 刻录机，可以用来刻录图像并连接到我的旧电脑上。旧电脑应该有几兆的内存，一个 DVD 阅读器，或者能够启动外部 DVD/拇指驱动器。你还需要准备好告别旧电脑上的任何数据，因为你将清除所有数据。

不言而喻，你需要一个显示器和键盘，至少在安装的第一阶段。我从我妻子的电脑里偷了一个显示器和键盘，并向她保证我用完后会把它们放回去。

有许多 NAS Linux 发行版，但是我将安装 Ubuntu 的普通服务器安装。这将使我有机会试用 20.04 版本，该版本将很快发布(在撰写本文时)。我将安装 GlusterFS 作为一个存储解决方案，这样它可以很容易地扩展和设置高可用性和故障转移。但是在这篇文章中，我将把重点放在单节点 NFS 服务器上，并安装在我的 Mac 上。

我将从 Ubuntu 20.04 的预发布版本开始(当你读到这篇文章时，它可能已经发布了)。[在这里](http://releases.ubuntu.com/20.04/)下载 ISO 镜像 [。一旦你有了映像，把它刻录到 DVD 上，把 DVD 放在旧电脑上，重新启动到引导菜单或 BIOS 菜单，然后选择要引导的 DVD。当它准备安装时，你应该会看到很多滚动线。它可能会问你是否要使用新的安装程序。我可能会建议不要这样做，因为我最初使用它时遇到了问题，而且它无法在我的电脑上运行。但是旧的安装程序运行良好。](http://releases.ubuntu.com/20.04/)

你会被问一堆问题，大部分你可以取默认答案。但是当您进入存储配置时，您需要划分挂载为`/`的操作系统部分和未挂载的存储部分。所以选择定制储物布局。

首先，如果您的主驱动器已经分区，选择它，并选择重新格式化。然后再次选择它，并选择添加 GPT 分区。大约 15gb 应该足够容纳操作系统和软件，因此设置为这个大小。将其格式化为`ext4`并将其安装在`/`。点击 Create，回到你的主屏幕，你应该有你的新分区列表。

再次选择您的主驱动器，并再次选择添加 GPT 分区。将大小设置为剩余大小，并在格式下，选择保留未格式化。点击 Create，在您的主屏幕上，您应该仍然只列出了单个挂载点。您的未格式化分区应该列为分区 3。在底部，分区 1 被列为引导区，分区被列为`/`挂载点。如果你想玩分布和故障转移，你可以做几个分区，但是我只做一个。点击 Done，然后确认您将用这些分区覆盖您的磁盘。

在下一个屏幕上，给你的计算机起一个名字，给你自己起一个用户名和密码。在下一个屏幕上，选择安装 Openssh 服务器，如果您有帐户，从 GitHub 导入您的 SSH 身份，并选择“允许通过 SSH 进行密码验证”我们需要允许这样做，因为有时 SSH 身份不能用这个安装程序正确地复制，我们需要使用一个密码将我们的密钥传递到我们的新计算机。如果你不知道我说的 SSH 和 keys 是什么，你可以在 GitHub 上找到[的说明。你需要任何一台电脑上的钥匙来和这台机器对话。](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

在特色软件屏幕上，不要选择任何内容。我们新的根分区足够大，可以只安装我们需要的软件，其余的可以用于网络存储。现在你可以在等待安装完成的时候尽情观看几集你最喜欢的流媒体连续剧。

![](img/7045f2ef425c2b75df0848935ce5bd88.png)

图片来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=620397) 的 [nile](https://pixabay.com/users/nile-598962/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=620397)

安装程序似乎在安装 SSH 密钥时遇到了问题，所以您可能需要自己复制它们。如果您有命令`ssh-copy-id`，那应该可以工作，如果没有，您将不得不使用`scp`。密钥是文本，所以你可以从你的`.ssh/id_rsa.pub`中剪切并粘贴密钥到主机的`.ssh/authorized_hosts`文件中。无论您如何操作，准备好密钥以便无需输入密码就可以连接是非常重要的，因为许多应用程序都依赖 SSH 在没有密码的情况下在可信主机之间进行通信。我给 Ubuntu 的人留了一张纸条，希望他们能修复密钥的安装。

我将尝试使用 [GlusterFS](https://www.gluster.org/) 系统，而不是简单的老式 NFS 服务器，那样太容易了。它提供了 NFSv3，我应该能够将它用作我的 Kubernetes 集群的存储提供者。它似乎是 Ubuntu 家族的正式成员，所以不需要添加源代码或导入密钥。命令`sudo apt-get install glusterfs-server`足以安装它。

关于 GlusterFS 使用的术语的一些注释。需要注意的一个关键点是，尽管它的名字如此，但它实际上并不是一个文件系统。*节点*是主机，而*可信池*是节点的集合，其中一个是主节点。因为我们只有一个节点，所以我们将使它变得非常简单。我们的受信任池只有一个节点，并且该节点是主节点。一个*砖块*是一个存储单元，所以每个节点可以有多个砖块。我们的只有一块砖，但以后添加外部驱动器和制造更多砖应该很容易。如果您找到另一台计算机并将其添加到系统中，您也可以添加更多节点。

让我们做一块砖吧！首先，找到我们在安装中创建的磁盘分区。`ls /dev`应该列出一切。我的设备被称为`sda3`，因为它是第一个磁盘(磁盘“a”)的第三个分区。我们需要格式化并安装它。(我们可以在安装时完成，但是我没有提前足够长的时间阅读。)以下是命令:

```
sudo mkfs.xfs /dev/sda3 # format an xfs filesystem
sudo vi /etc/fstab # add: /dev/sda3 /export/sda3 xfs defaults 0 0 
sudo mkdir -p /export/sda3 # make the mount directory
sudo mount -a # mount from /etc/fstab
sudo mkdir -p /export/sda3/brick # make a brick!
```

不管出于什么原因，安装 GlusterFS 并没有启动守护进程，所以我们需要用`systemctl start glusterd`来完成。然后，我们可以创建卷并启动它:

```
sudo gluster volume gv0 artful:/export/sda3/brick
sudo gluster volume start gv0
sudo gluster volume info
```

在上面的例子中，`artful`是我在安装时给我的电脑取的名字，`gv0`是我给这个卷取的名字。最后一个命令应该打印出如下内容:

这显示了一点问题。`nfs.disable`开启。如果你尝试打开它，它会告诉你格鲁斯特·NFS 服务器组件已经被弃用，取而代之的是 NFS·甘尼萨。我试着打开它，但是我的 MacOS NFS 客户端不喜欢 Gluster 提供的 NFS v3，所以我必须安装 NFS·甘尼萨。

我可以从一开始就安装 NFS·甘尼萨，但是它只有很少的文档，所以我选择了格鲁斯特。另外，这个项目的另一个目的是让我可以在我的 Kubernetes 集群上使用存储。现在，我想我两样都要。你能看出我是在编故事吗？有时，将这种类型的架构放在一起就像是在放拼图。在某些时候，你不得不尝试每一件衣服，直到你找到合适的。

不过，抱怨够多了。让我们安装 NFS·甘尼萨。事实证明，它也是 Ubuntu 家族的正式成员，不需要添加源代码来安装它。只需安装两部分:

```
sudo apt-get install nfs-ganesha
sudo apt-get install nfs-ganesha-gluster
```

这给了我们新的 NFS 服务器和它与 Gluster 对话的接口。现在我们可以为 NFS·甘尼萨设置一个配置来导出 Gluster 卷。Ganesha 的主要配置在`/etc/ganesha/ganesha.conf`中，我们将添加一个`exports`目录，创建一个配置文件，并将其包含在`ganesha.conf`中

```
sudo mkdir /etc/ganesha/exports
sudo vi /etc/ganesha/exports/gv0.conf
```

然后将以下内容添加到`gv0.conf`文件中:

然后将行`%include /etc/ganesha/exports/gv0.conf`添加到`/etc/ganesha/ganesha.conf`的末尾。现在用`sudo systemctl restart nfs-ganesha`重启甘尼萨。

回到您的 Mac，在您的主目录中创建一个目录来挂载到:`mkdir gluster`，然后您可以使用`mount -t nfs -o soft,intr,rsize=8192,wsize=8192,timeo=900,proto=tcp,vers=4 <hostname>:/ /Users/<yourname>/gluster`挂载新的挂载点，其中< hostname >是您的新 NFS 服务器的名称，< yourname >是您的 Mac 用户名。

现在把它测试出来:`cd`到挂载的目录下并生成一个文件。回到您的 NFS 服务器，您可以转到您为 GlusterFS 创建的目录，您应该能够看到这个新文件。

我的周末项目做完了，正好周末接近尾声。而且来得正是时候——我得把我妻子的显示器放回去，这样她明天就可以工作了。在我的下一篇文章中，我将把这个新的存储解决方案添加到我在 [Kubernetes 从零开始](https://medium.com/better-programming/kubernetes-from-scratch-4691283e3995)创建的 Kubernetes 集群中。尽情享受你的新储物盒吧！