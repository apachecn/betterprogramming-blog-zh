# 在 Linux 和 Windows 上用 Molecule 在本地测试角色

> 原文：<https://betterprogramming.pub/testing-ansible-roles-locally-with-molecule-on-linux-and-windows-a0903ec92496>

## ansi ble | Testing | molecular | lib virt | Docker

## 加快您的开发周期，满怀信心地发布

![](img/62d2249c8109490ad52302a1e2af9e33.png)

[瑞兰德·迪恩](https://unsplash.com/@ryland_dean?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# Ansible 面临的挑战

在我的职业生涯中，无论我在哪里遇到 Ansible，使用它的人总是面临同样的挑战。虽然每个人都希望有一个可行的设置，允许以可靠、可重复和灵活的方式在服务器(或虚拟机)群中部署配置和软件安装，但现实是这通常会很快变成任务、角色和行动手册的交织集合。既要保证高质量，又要保持干净简洁，很难。

我经历过的主要有以下几种情况:

*   “瞎”写任务，然后应用到主机上；迭代，直到一切运行无误
*   对剧本或角色进行小的修改，然后将它们应用到主机，然后忘记将它们提交给 git
*   在现有的已配置的主机上测试您的角色，并忘记新安装的主机所需的依赖关系
*   构建和运行具有许多角色的剧本，但是从来没有独立地测试角色和跨角色重用变量或全局变量，这使得很难或几乎不可能运行单个角色，然后用每个角色的标签来解决这个问题。
*   手动和按需运行行动手册。导致主机上的配置漂移，例如，由于缺少 git 提交。

# 如何避免混乱

灵活性是巨大的，能够快速反应是当今成功的关键因素。然而，如果没有一些基本的分离和指导方针，一个可行的存储库会很快变成一个技术部门的庞然大物。

最重要的事情之一是编写单独的、可重用的角色。即使你不打算在公司内部分享它们，它也有助于专注于特定角色的目的。就像在微服务世界中，你不希望一个角色负责多件事情。一个角色的单独的 repo 还允许您单独开发和测试它，以确保高质量的标准。

*   每个角色一个存储库，独立测试
*   一个或多个剧本库，引用角色

在你重新发明轮子之前——看看`ansible-galaxy`适合你需求的公共开源角色。

在您的剧本库中，您可以很容易地用一个`requirements.yml`文件引用角色，也可以从 git 中提取角色:

```
# ansible-galaxy install -r requirements.yml
roles:
  - name: geerlingguy.docker
    version: 4.2.4
  - name: https://github.com/MyOrganization/repo_name.git
    type: git
    version: master
```

# 如何保证质量

就像开发人员通过 git 使用 lint、运行测试和构建工件的管道来确保其软件项目的质量一样，DevOps(或 GitOps)可以使用相同的原则来确保高质量、可重复和自动化的部署机制。

这里的中心思想是在定义好的、可重复的环境中验证你的工作结果，通常是“代码”，以确保它也适用于你的同事，并且结果是可靠的和可重复的。一旦您验证了可重现的结果，您就可以自动部署，以确保您的服务器群状态一致或检测配置偏差。

# 介绍``DrPsychick/ansible-testing`

Molecule 允许您针对为测试而设置的多个实例来测试您的 Ansible 角色。虽然 Molecule 可以为您构建 Docker 容器，但它专注于运行和测试 Ansible 角色，而不是提供运行测试的实例。为了使集成到 Molecule 中变得非常容易，我最近发布了一个 Ansible role，您可以使用它来定义和提供测试所需的实例，无论是 Linux (Fedora，Ubuntu 等)。)或 Windows(通过 libvirt)。

对于以下示例，您需要的是:

*   安装了`Docker`的 Linux 主机
*   `sudo`特权
*   Python 库`molecule molecule-docker`

> 这个`DrPsychick/ansible-testing`角色非常适合我的用例，我希望它能很好地为你服务。如果您的用例不同或需要调整，请通过创建问题和打开拉动式请求来为角色做出贡献。

[](https://github.com/drpsychick/ansible-testing) [## GitHub-DrPsychick/ansible-testing:一个 ansi ble 角色，允许为…

### 允许创建 SystemD 实例来测试角色的一个角色

github.com](https://github.com/drpsychick/ansible-testing) 

# 分五步开始测试

做这个动手“教程”只需要五分钟。完成后，您就有了第一个用 Molecule 测试的 Linux Ansible 角色。在两个实例上使用空角色运行一次完整的`molecule test`大约需要 30 秒。

## 1.创建一个角色

用`molecule`创建一个新的空角色。

```
molecule init role demo.testrole
cd testrole
```

> 角色和名称空间限制:
> 
> 名称空间:[https://galaxy . ansi ble . com/docs/contributing/namespaces . html # galaxy-namespace-limits](https://galaxy.ansible.com/docs/contributing/namespaces.html#galaxy-namespace-limitations)
> 角色:[https://galaxy . ansi ble . com/docs/contributing/creating _ Role . html # Role-names](https://galaxy.ansible.com/docs/contributing/creating_role.html#role-names)

如果您有一个现有的角色，您可以使用以下命令让 Molecule 初始化默认场景:

```
# creates and populates ./molecule/default/
molecule init scenario default
```

## 2.从 GitHub 获取分子示例

从`DrPsychick/ansible-testing`为您的角色下载示例文件([https://github . com/DrPsychick/ansi ble-testing/tree/master/docs/molecule/default](https://github.com/DrPsychick/ansible-testing/tree/master/docs/molecule/default)):

```
for f in create destroy molecule requirements vars; do 
  curl -o molecule/default/$f.yml "https://raw.githubusercontent.com/DrPsychick/ansible-testing/master/docs/molecule/default/$f.yml"; 
done
```

在运行它之前，您可能想要根据您的需要调整`molecule/default/vars.yml`和`molecule/default/molecule.yml`，这允许您配置`DrPsychick/ansible-testing`角色并定义您想要测试的容器实例。

```
# vars.yml
# a temporary directory that will be deleted with 'destroy'
work_dir: "/tmp/ansible-demo.testrole-default"
containers:
  - name: ubuntu2204
    os: ubuntu
    dockerfile: Dockerfile_Ubuntu
    files:
      - "entrypoint.sh"
    args:
      VERSION: 22.04
```

确保集装箱名称与`molecule.yml`中的`platforms`相符:

```
[...]
platforms:
  - name: ubuntu2204
[...]
```

## 3.在你的角色上运行分子

因为您可能以用户身份登录，所以当您第一次运行 Molecule 时，可能会出现以下错误:

```
fatal: [localhost]: FAILED! => {"changed": false, "module_stderr": "sudo: a password is required\n", "module_stdout": "", "msg": "MODULE FAILURE\nSee stdout/stderr for the exact error", "rc": 1}
```

这是因为`ansible-testing`角色确保所有[先决条件](https://github.com/DrPsychick/ansible-testing/blob/master/tasks/dependency.yml)都已安装。通过运行`sudo echo`解决这个问题，然后再次运行分子。

```
# create instances (with DrPsychick/ansible-testing)
# this will take a while the first time, as it builds 
# the docker images.
# Once built, creating instances takes only seconds.
molecule create
# list instances or check systemd status
docker ps
docker exec -it ubuntu2204 systemctl status
# run your role on the instances
molecule converge
molecule idempotence
```

## 4.发展你的角色

现在，您可以脱离并实现您的角色了。用于一个或多个操作系统或风格。根据需要经常重新运行`molecule converge`，以验证您的任务正在做它们应该做的事情。在您结束工作之前，确保一个`molecule test`也能顺利完成，因为这可以确保您的角色在新安装的实例上工作。

同样重要的是，您要确保您的角色是等幂的——这意味着当您第二次运行它时，要确保它不会报告任何变化。为什么这如此重要？当您稍后自动化 Ansible 运行时，您想要为一个系统定义目标状态，并且您想要所有系统具有相同的配置状态。

例如，为了检测配置漂移(主机的配置偏离了期望的状态)，您可以在`--check`模式下运行一个 Ansible playbook 来查看是否会进行任何更改。这只有在您的角色以幂等的方式编写(并且`--check`安全)时才有可能。

您可以使用`molecule/default/prepare.yml`为您的角色准备实例，以防您需要像安装其他角色一样的特定先决条件。您可以在最后使用`molecule/default/verify.yml`来运行任务，以验证您的角色的配置或安装是否按预期工作。

## 5.在管道中运行分子

既然您已经设置并配置了分子场景，开发并测试了您的角色，那么是时候通过在您的管道中运行分子测试来持续确保高质量了。

由于有许多不同的 CI/CD 解决方案，我将只快速提及运行 Molecule 的要求(上面已经提到了)

*   Python3 `ansible ansible-lint docker molecule molecule-docker`
*   在管道中运行特权 Docker 容器的可能性(可能使用 Docker-in-Docker，`docker:dind`)

【2023 年 3 月更新:请务必检查我的`ansible-runner`图像，以便在管道中使用 Ansible:

[](https://github.com/SickHub/docker-ci-images) [## GitHub-SickHub/docker-ci-images:docker:dind with buildx included/kubernetes images with kind…

### 可以在基于 docker 的 CI 管道中使用的图像集合。而不是将依赖项安装在…

github.com](https://github.com/SickHub/docker-ci-images) 

# 贡献给``DrPsychick/ansible-testing``

公开问题，以表明功能请求、缺陷，或者只是显示您的兴趣并分享您正在处理的特定问题或案例。

根据您的需求调整角色并使用您的本地版本很简单:

1.  在 GitHub 上分叉项目
2.  看看你的 Linux 盒子上的叉子
3.  符号链接你的角色分子场景中的叉(即`molecule/default`)
4.  注释掉`requirements.yml`中的角色并删除本地副本

现在，您的角色目录中的所有`molecule`命令都将使用您的本地分支。

下面是详细描述:[https://github.com/DrPsychick/ansible-testing#contributing](https://github.com/DrPsychick/ansible-testing#contributing)。

# 参考

[](https://levelup.gitconnected.com/test-windows-ansible-roles-on-local-libvirt-vms-within-minutes-e5dfa031bc4) [## 在几分钟内测试本地 libvirt 虚拟机上的 Windows Ansible 角色。

### 在几分钟内启动 Windows 虚拟机，测试您的可行角色。

levelup.gitconnected.com](https://levelup.gitconnected.com/test-windows-ansible-roles-on-local-libvirt-vms-within-minutes-e5dfa031bc4) [](https://github.com/DrPsychick/ansible-testing) [## GitHub-DrPsychick/ansible-testing:一个 ansi ble 角色，允许为…

### 允许创建 SystemD 实例来测试角色的一个角色

github.com](https://github.com/DrPsychick/ansible-testing) [](https://molecule.readthedocs.io/en/latest/) [## 可翻译的分子-分子文档

### Molecule 只支持 Ansible 的最新两个主要版本(N/N-1)，也就是说如果最新版本是 2.9.x…

分子. readthedocs.io](https://molecule.readthedocs.io/en/latest/) [](https://drpsychick.org/drpsychick-on-the-web-a9ccfb0df17e) [## 网上心理咨询

### 我所在的所有网站的一个简单的“登陆页”。

drpsychick.org](https://drpsychick.org/drpsychick-on-the-web-a9ccfb0df17e) 

感谢您的时间和兴趣！

```
**Want to Connect?**If you want to support me, sign up for Medium through my membership link: [*https://drpsychick.org/membership*](https://drpsychick.org/membership) or visit me on GitHub.
```