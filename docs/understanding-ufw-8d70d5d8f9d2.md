# 了解 Ubuntu 中的简单防火墙(UFW)

> 原文：<https://betterprogramming.pub/understanding-ufw-8d70d5d8f9d2>

## 人类的 Ubuntu 防火墙；创建基于 IPv4 或 IPv6 主机的防火墙的用户友好方式

![](img/966a7ef4fb6a5e5c044ee4c052b89552.png)

我一直害怕 iptables。如果你想知道原因，请查看这个页面。

尽管我从许多人那里听说 iptables 更健壮、更安全，但我从未使用过，因为它总是令人望而生畏。

我个人认为，如果我对 iptables 之类的东西感到不舒服，并且不管不顾地使用它，我可能会增加更多的安全漏洞，而不会利用它提供的好处。

所以，我现在坚持使用 UFW。这也是我喜欢 Ubuntu 胜过其他版本的原因。熟悉产生自信，我知道我会少犯错误。

不要认为这是 Ubuntu 对其他更安全版本的提升，因为事实并非如此。这只是我个人的喜好。

边注:你知道 *Ubuntu* 一般翻译成[“我是因为我们是](https://en.wikipedia.org/wiki/Ubuntu_philosophy)”吗？

# 在开始之前

请记住这些事情:

1.  使用某种形式的防火墙。如果不是 UFW，你可以直接使用 iptables。
2.  如果您使用的是 UFW，请确保您的 UFW [服务在重启时启动。](https://medium.com/@gokulnk/linux-systemctl-46bd0a11e27b)
3.  充分理解 UFW 的违约行为。
4.  将所有内容列入黑名单，将需要的内容列入白名单总是更好的选择。
5.  设置一个像 Zabbix 这样的监控工具，当 UFW 倒下时，它会给你一个触发器。

# 入门指南

## 安装 ufw

```
sudo apt install ufw
```

## 检查状态

```
# ufw status
Status: inactive
```

我们将在添加相关规则后启用`ufw`。

## **白名单** SSH

在启用 UFW 之前，请确定您允许 SSH，以便您可以使用 SSH 从任何地方访问您的服务器。

```
#sudo ufw allow ssh
Rules updated
Rules updated (v6)
```

## 检查添加的规则

当 UFW 未激活时，您不能使用`ufw status`检查添加的规则。相反，你可以使用`ufw show added.`你甚至可以在启用 UFW 后使用它。

```
# ufw show added
Added user rules (see 'ufw status' for running firewall):
ufw allow 22/tcp
```

## 启用 UFW

启用 UFW 而不添加 SSH 规则可能会将您锁定在服务器之外。因此，启用 UFW 时要小心。但是我没有试过，所以我不能确定。

```
# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
```

## 检查状态

`ufw status`显示 UFW 的状态并列出所有已启用的规则。

```
# ufw status
Status: activeTo                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
```

`ufw status`可能有问题，因为它没有给出所有的细节。查看下一部分。

# UFW 违约

前几天，不知道违约让我花了几个小时。

由于没有显示默认值，并且`Action` 下的细节不够清楚，我假设了一些事情，这让我付出了高昂的代价。

因此，在为您的应用程序实际设置相关规则之前，请仔细阅读默认选项。

您可以使用`ufw status verbose`获得这些细节。

```
# ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skipTo                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
```

正如您从输出中看到的:

1.  默认值是`deny (incoming)`:这将确保没有外部系统可以连接到您的机器，直到您为它添加一个覆盖规则。
2.  默认值为`allow (outgoing)` : 这意味着所有传出的请求都被启用。该设置有助于您顺利运行`apt-install`、`wget`和`ping`等命令。但是，如果你想保持你的服务器安全，最好是改变默认阻止传出，然后允许您需要的特定 IP/域。
3.  默认为`disabled (routed)`。这意味着所有路由被禁用，转发被阻止。如果您不将您的机器用作路由器，这是一个很好的默认设置。
4.  正如您在`Action`栏中看到的，它是`ALLOW IN`。也就是说还有`ALLOW OUT`。如果您将默认值更改为`deny (outgoing)`，您需要添加一个这样的规则。

# 更改默认值

我们在上面看到的默认值相当于以下规则:

```
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

如果您想将默认值更改为拒绝传出，您可以运行:

```
#sudo ufw default deny outgoing
Default outgoing policy changed to 'deny'
(be sure to update your rules accordingly)
```

如果设置上述默认值，您将需要手动添加访问外部系统的规则。这可能是一个繁琐的过程，但安全得多。

例如，假设您希望允许端口 10060 上的传出流量。您可以运行:

```
ufw allow out 10060
```

与其保持`outgoing`默认不变，我觉得还不如拒绝传出。每当您想要执行升级或安装软件时，您可以临时添加一个规则，然后在完成后将其删除。

还有，如果你想只打开特定的端口，这样你就可以使用 apt，你可以使用我从这个回答中借用的[下面的规则。](https://serverfault.com/questions/468907/ufw-blocking-apt/736775#736775)

```
ufw default deny incoming
ufw default deny outgoing
ufw limit ssh
ufw allow svn
ufw allow git
ufw allow out http
ufw allow in http 
ufw allow out https
ufw allow in https
ufw allow out 53
ufw logging on
ufw enable
```

# 显示规则

您可以使用`ufw show added`来显示所有添加的规则。

```
# ufw show added
ufw allow 22/tcp
ufw allow from x.x.x.x to any port 27017
ufw allow from x.x.x.x to any port 27017
ufw allow from x.x.x.x to any port 10050
ufw allow from x.x.x.x to any port 10050
```

之前，我使用的是命令`ufw status numbered`，但是现在我使用`ufw show added`，然后使用那里的规则进行删除，就像这样:

```
ufw delete allow 22/tcp
```

# 经验法则

1.  确保 UFW 是启动的。
2.  根据您的舒适程度，更改默认值，使其更具限制性。
3.  默认情况下拒绝，只启用需要的内容。
4.  让你的规则尽可能的具体。例子:`sudo ufw allow from 192.168.0.0/24 to any port 22 proto tcp`。
5.  添加一个监控工具，如 Zabbix，检查 UFW 的状态，以及任何关键的规则。

# 结论

如果你想要更多的细节和查询选项，请查看 Ubuntu 文档。

我犯过很多错误，因为我对 UFW 不够了解。我希望这篇文章能防止你犯这样的错误。

感谢阅读！