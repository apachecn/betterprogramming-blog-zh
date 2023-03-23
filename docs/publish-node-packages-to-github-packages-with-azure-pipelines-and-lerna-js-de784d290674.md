# 使用 Azure 管道和 Lerna.js 将节点包发布到 GitHub 包

> 原文：<https://betterprogramming.pub/publish-node-packages-to-github-packages-with-azure-pipelines-and-lerna-js-de784d290674>

## 发布节点包的实践指南

![](img/5b773f4fef8d0f53f8af6554cb4622ee.png)

在本文中，我们将研究两种将 Node.js 包发布到 GitHub 包的方法；和 NPM 一起出版，然后是勒纳。

[本文中使用的代码可以在这里找到。](https://github.com/cjh-cloud/github-node-package)

# 使用 Azure Pipelines 和 NPM 将节点包发布到 GitHub 包

## 图书馆

创建一个名为“GitHub Packages”的库(在一个新的 Azure DevOps 项目中)，使用变量`GH_TOKEN`和一个具有`write:packages`权限的 GitHub 个人认证令牌的值。通过单击挂锁图标设置此变量 secret 的值🔒。

![](img/ce427aedb0876ee87c155f6bd0052203.png)

## 密码

`.azure/publish.yml` —该文件定义管道，安装 Node.js 14.x 并创建一个`.npmrc`文件，将 GitHub Packages 注册表设置为发布包的目的地，运行`npm publish`发布该包。

```
name: Publish Node GitHub Package

trigger:
  - main

pr: none

variables:
  - group: GitHub Packages
  - name: vmImageName
    value: 'ubuntu-latest'

stages:
  - stage: Publish
    displayName: Publish GitHub Package
    jobs:
    - job: Publish
      displayName: Publish Job
      pool:
        vmImage: $(vmImageName)
      steps:
      - task: NodeTool@0
        inputs:
          versionSpec: '14.x'
        displayName: 'Install Node.js version 14.x'

      - bash: |
          echo //npm.pkg.github.com/:_authToken=${GH_TOKEN} > ~/.npmrc
          npm publish
        env:
          GH_TOKEN: $(GH_TOKEN)
```

`package.json` —添加一个`publishConfig`部分来定义要发布到的 GitHub 注册表

```
"publishConfig": {
  "registry": "https://npm.pkg.github.com/[User or Org name]"
},
```

# 使用 Lerna 发布版本

源代码可以在这里找到:[https://github.com/cjh-cloud/github-node-package](https://github.com/cjh-cloud/github-node-package)

## 图书馆

这与上面使用的库很大程度上是相同的，但是增加了`GH_USER`，它只是 GitHub 用户的包注册中心的用户名，用来发布 Node.js 包。

![](img/e69e7fd9ebe154ceb3f6e43206b478fc.png)

## 密码

`package.json` —同上，增加了`publishConfig`部分。

`.azure/publish.yml` —这个管道还有几个步骤:

*   检出`main`分支
*   配置 Git 凭证，以便 Lerna 可以制作 Git 标记并释放
*   Boostrap Lerna(为所有软件包安装节点模块)
*   用`--registry https://npm.pkg.github.com`将包发布到 GitHub 包

```
name: Publish Node GitHub Package

trigger:
  - main

pr: none

variables:
  - group: GitHub Packages
  - name: vmImageName
    value: 'ubuntu-latest'

stages:
  - stage: Publish
    displayName: Publish GitHub Package
    jobs:
    - job: Publish
      displayName: Publish Job
      pool:
        vmImage: $(vmImageName)
      steps:

      - bash: git checkout main
        displayName: 'Checkout main branch' # to avoid detached head

      - bash: |
          git config user.name "$GH_USER"
          git remote rm origin && git remote add origin https://random_str:${GH_TOKEN}@github.com/${GH_USER}/github-node-package.git && git fetch
        displayName: 'Configure git'
        env:
          GH_TOKEN: $(GH_TOKEN)

      - task: NodeTool@0
        inputs:
          versionSpec: '14.x'
        displayName: 'Install Node.js version 14.x'

      - bash: yarn install
        displayName: Install Yarn dependencies

      - bash: yarn lerna bootstrap
        displayName: Bootstrap Lerna

      - bash: |
          echo //npm.pkg.github.com/:_authToken=${GH_TOKEN} > ~/.npmrc
          yarn lerna publish --yes --exact --conventional-commits --force-publish --create-release github --registry https://npm.pkg.github.com/
        env:
          GH_TOKEN: $(GH_TOKEN)
```

希望你觉得这有用！