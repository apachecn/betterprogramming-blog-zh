# 23 个 Linux 服务器安全提示和最佳实践

> 原文：<https://betterprogramming.pub/23-linux-server-security-tips-and-best-practices-b8c59b9b9e3e>

## 系统管理员应该遵循的清单

![](img/3b4e8ed55daa6d75ddd3849b8ffa0676.png)

由[赛·基兰·阿纳加尼](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

本文包括一组命令和最佳实践，可以用来提高 Linux 服务器(RHEL/CentOS)的安全性。如果你有更多的建议，请在评论中提及。享受阅读吧！

## 1.删除不安全的工具(FTP、telnet、rlogin、rsh 等)。)并只使用安全的替代方案(SCP、SSH、sftp、rsync 等)。)

选择数据通信工具时，请仅使用安全和加密的工具，并将其余工具从服务器上移除。

```
$ yum erase xinetd ypserv tftp-server telnet-server rsh-server
```

## 2.启用防火墙/Iptables

Iptables 允许您配置 Linux 内核防火墙的 IP 包过滤规则。为了获得最大的优势，您可能需要一些关于设置这些规则的高级知识。下面是几个你可以尝试的例子。

```
$ service iptables start// Force SYN packets check
$ iptables -A INPUT -p tcp ! --syn -m state --state NEW -j DROP// Drop XMAS packets
$ iptables -A INPUT -p tcp --tcp-flags ALL ALL -j DROP// Drop null packets
$ iptables -A INPUT -p tcp --tcp-flags ALL NONE -j DROP// Drop incoming packets with fragments
$ iptables -A INPUT -f -j DROP
```

## 3.禁用不需要的服务和守护程序

大多数时候你可能不需要像 AutoFS，NFS，FTP，HTTP，NIS，telnetd，sendmail 这样的服务。您可以删除/禁用它们。

```
$ chkconfig –list | grep ‘3:on’
$ service serviceName stop
$ chkconfig serviceName off$ yum remove packageName
```

## 4.定期审核已安装的软件包并删除不需要的软件包

较少数量的包总是导致较小的安全威胁面，这意味着最终潜在的威胁更少。因此，只保留必要的包，并清理其余的包。

```
$ yum list installed
$ yum list packageName
$ yum remove packageName
```

## 5.定期审核监听网络端口并拦截不需要的端口

有时，您可能会为不同的服务打开端口，并在以后将其删除。这是非常危险的，所以你必须时刻注意你打开的端口，并确保不需要的端口被阻塞。

```
// List open ports of own server
$ netstat -tulpn
$ ss -tulpn// List open ports of any server in network 
$ nmap -sT -O localhost
$ nmap -sT -O server.example.com// Use chkconfig to disable corresponding services
$ chkconfig –list | grep ‘3:on’
$ service serviceName stop
$ chkconfig serviceName off
```

## 6.定期审核用户帐户并禁用不需要的帐户

仅添加所需的最少数量的用户帐户，并禁用其余帐户。

```
// Lock password (add ! in the second field of the file /etc/passwd, password authentication will fail because of this, but other authentication methods like SSH keys will still work)
$ usermod -L userName
$ passwd -l userName// Unlock password (i.e. revert the effect of above command)
$ passwd -u userName// Expire account 
$ chage -E0 userName// Change default shell to non-interactive, so user won't get any login shell
$ usermod -s /sbin/nologin userName// Verify locked user account (*LK* flag in the output indicates that the account is locked)
$ passwd --status userName
userName ***LK*** 2021-04-05 0 45 7 -1 (Password set, SHA512 crypt.)// Verify password and account expiration
$ chage -l userName
Last password change: Jan 19, 2021
Password expires: Jan 02, 2022
Password inactive: never
Account expires: Jan 02, 2022
Minimum number of days between password change: 0
Maximum number of days between password change: 45
Number of days of warning before password expires: 7// Verify non-interactive shell
$ grep ^userName /etc/passwd
userName:x:1000:1000:,,,:/home/userName:/sbin/nologin
```

## 7.启用 SELinux

安全性增强的 Linux (SELinux)是由内核提供的一种访问控制安全机制。

```
// View current status
$ sestatus
$ system-config-selinux
$ getenforce// Enable SELinux (using command)
$ setenforce enforcing
$ setenforce 1// Enable SELinux (by editing config file)
$ vi /etc/selinux/config
```

## 8.关闭 IPv6

除非出于特定原因使用 IPv6，否则您可以禁用它。

```
$ vi /etc/sysconfig/networkNETWORKING_IPV6=no
IPV6INIT=no
```

## 9.将/boot 目录设为只读

默认情况下，`/boot`目录允许读写访问。因为它包括 Linux 内核及其相关文件，所以您必须保护它们免受未经授权和无意的修改。

```
$ vi /etc/fstabLABEL=/boot     /boot     ext2     defaults,ro     1 2
```

因此，您将来可能无法升级内核。在这种情况下，您可以暂时恢复此更改。

## 10.禁用 ICMP 广播请求并强化/etc/sysctl.conf

[Ping](https://en.wikipedia.org/wiki/Ping_(networking_utility)) 是检查服务器是否可以通过网络到达的最基本方式。Ping 操作的方式是向目标主机发送 ICMP(互联网控制消息协议)回应请求数据包，并等待 ICMP 回应回复。它仅用于测试和故障排除。因此，当没有这种需要时，您必须禁用它。此外，最好使用以下配置强化`/etc/sysctl.conf`。

```
$ vi /etc/sysctl.conf# Ignore ICMP request
net.ipv4.icmp_echo_ignore_all = 1# Ignore Broadcast request
net.ipv4.icmp_echo_ignore_broadcasts = 1
net.ipv4.icmp_ignore_bogus_error_messages=1# Turn on execshield
kernel.exec-shield=1
kernel.randomize_va_space=1# Enable IP spoofing protection
net.ipv4.conf.all.rp_filter=1# Make sure spoofed packets get logged
net.ipv4.conf.all.log_martians = 1# Disable IP source routing
net.ipv4.conf.all.accept_source_route=0// Load new changes
$ sysctl -p
```

## 11.加强密码策略

绝不允许用户帐户的密码为空。

```
// Check for empty password accounts
$ cat /etc/shadow | awk -F: ‘($2==””){print $1}’
```

此外，使用 pam_cracklib (PAM =可插拔认证模块)等工具强制使用强密码。

```
$ vi /etc/pam.d/system-authpassword requisite pam_cracklib.so retry=3 minlen=8 lcredit=1 ucredit=2 dcredit=2 ocredit=1// Notation
retry = retry attempts for a user to pick a good password before the passwd program aborts
lcredit = lower-case
ucredit = upper-case
dcredit = numeric (digit)
ocredit = non-alphanumeric (other)
minlen = minimum length
```

## 12.监控可疑的用户活动

如果您注意到可疑的用户活动，或者必须与具有不同信任级别的难缠用户打交道，您可以使用`psacct`或 acct 工具收集有关他们活动和流程的信息，以便进行事后审计。

```
$ yum install psacct// Enable
$ chkconfig psacct on
$ /etc/init.d/psacct start
$ /etc/init.d/psacct status// Find day-wise login info for a user
$ ac -d userName// Find last executed commands by a user
$ lastcomm userName// Find unsuccessful login attempts
$ faillog -u userName
```

## 13.禁用 SSH 的 root 登录和密码验证

默认情况下，Root 用户在大多数 Linux 服务器上都是可用的，黑客经常试图通过猜测密码以 root 身份登录。因此，建议禁用 root 帐户。首先，确保您有一个管理员用户帐户，这样您以后就可以使用`sudo`特权访问命令执行根级命令。然后，您可以安全地禁用 root 登录。

```
$ vi /etc/ssh/sshd_configPermitRootLogin no
```

此外，您可以禁用密码身份验证，而采用安全的方法，比如 SSH 公私密钥身份验证。

```
$ vi /etc/ssh/sshd_configPasswordAuthentication no
```

## 14.三指敬礼时禁用关机/重启(Ctrl+Alt +Del)

您可以定义 Linux 系统必须如何对三指敬礼做出反应，但是因为有人也可能错误地运行它，所以最好在总是运行的服务器上禁用它。

```
$ vi /etc/inttab// Comment-out the line starting with `ca::ctrlaltdel:`
# ca::ctrlaltdel:/sbin/shutdown -t3 -r now
```

## 15.删除桌面

你真的不需要在专用服务器上运行桌面。禁用它们不仅可以提高性能，还可以提高安全性(更少的膨胀软件/包=更少的安全问题)。

```
$ yum groupremove "X Window System"
$ yum groupremove "GNOME Desktop"
$ yum groupremove "KDE Plasma Workspaces"
$ yum groupremove "Server with GUI"
$ yum groupremove "MATE Desktop"
```

## 16.修复全局可写文件和无主文件的权限

查找权限松散的文件，并设置正确的用户和组权限。

```
// Find all world writable and sticky bits set files
$ find /dir -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print// Find all files not owned by any user or group
$ find /dir -xdev \( -nouser -o -nogroup \) -prin
```

## 17.定期清除不需要的文件/脚本/目录，保持服务器清洁

如果您创建了一个临时文件/脚本/文件夹，请确保尽快删除它。不要在服务器上堆积大量的旧文件数据，因为某个地方可能隐藏着威胁。

## 18.使服务器保持最新

始终应用最新的安全更新、版本、补丁等。到你的服务器上。

```
// Update all packages
$ yum updates// Update security-related packages
$ yum update --security// Check available updates
$ yum check-update
```

## 19.定期收集系统日志

系统日志帮助您了解服务和软件包中最可疑的行为和错误。主动收集系统日志信息，以便将来对安全事件进行潜在调查。

## 20.为重要文件/目录设置定期备份和安全的异地挂载点

在发生入侵或勒索软件攻击时，为了确保关键数据保持可访问，可以使用各种备份策略。根据经验，备份至少有三份副本，其中至少两份保存在不同的位置，一份保存在异地(3–2–1 备份规则)。

像 rsync 这样的工具可以帮助备份 Linux 中的数据，以及许多数据复制和备份相关的功能。

```
$ yum install rsync
```

## 21.在网络级别配置入侵防御工具

像 [Fail2Ban](https://en.wikipedia.org/wiki/Fail2ban) 这样的工具可以改变防火墙规则，禁止任何试图登录特定次数的地址。此外，它还可以用于发现和解决身份验证失败模式，并支持电子邮件警报。

```
$ yum install fail2ban
```

## 22.定期执行安全审计

像 auditd 这样的工具可以帮助收集基本的系统审计信息，并在磁盘上保存一个记录，例如，在`/etc/audit.rules`中的预定义规则。

然而，使用专门的商业工具运行安全审计始终是行业最佳实践，尤其是在企业环境中。通过这些审核，可以发现错误配置、过期策略、待定更新和罕见威胁。

## 23.提高物理安全性

在 BIOS 中禁止从 CD/DVD、外部设备和软驱引导。此外，启用 BIOS 密码并使用密码保护 GRUB，以限制对系统的物理访问。

*敬请关注下一个编程技巧。在那之前，祝你黑客生涯愉快！*

## 如果你喜欢这篇文章，你可能也会喜欢阅读:

[](https://medium.com/platform-engineer/microservices-design-guide-eca0b799a7e8) [## 微服务设计指南

### 大家都听说过微服务。但是你知道怎么设计一个吗？

medium.com](https://medium.com/platform-engineer/microservices-design-guide-eca0b799a7e8) [](/application-logging-best-practices-a-support-engineers-perspective-b17d0ef1c5df) [## 企业应用程序日志记录最佳实践(支持工程师的视角)

### 大家来写点大家都爱的有意义的日志吧！

better 编程. pub](/application-logging-best-practices-a-support-engineers-perspective-b17d0ef1c5df) [](/12-linux-commands-worth-remembering-for-log-file-operations-with-examples-4a861b6faaa3) [## 对于日志文件操作，值得记住的 12 个 Linux 命令(带示例)

### 系统管理员和支持工程师喜欢的工具列表

better 编程. pub](/12-linux-commands-worth-remembering-for-log-file-operations-with-examples-4a861b6faaa3)