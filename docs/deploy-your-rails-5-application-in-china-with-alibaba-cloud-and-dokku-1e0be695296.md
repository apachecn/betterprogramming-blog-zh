# 用阿里云和 Dokku 在中国部署你的 Rails 5 应用

> 原文：<https://betterprogramming.pub/deploy-your-rails-5-application-in-china-with-alibaba-cloud-and-dokku-1e0be695296>

## 使用 Docker 简化 Rails 部署

![](img/de70c64d38807616fb6a5be63f5868b4.png)

马丁·w·柯斯特在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上的照片

# Docker 是什么？

[Docker](https://opensource.com/resources/what-docker) 是一个工具，旨在通过使用容器来简化应用程序的创建、部署和运行。容器允许开发人员将应用程序与它需要的所有部分打包在一起，比如库和其他依赖项，然后作为一个包发送出去

# 什么是 Dokku？

Dokku 是 Docker 和 Heroku 的爱子——最初为 Ruby on Rails 创建的托管解决方案。 **Docker + Heroku = Dokku** ，看到了吗？使用 Dokku，您可以轻松地在 Heroku 上部署应用程序(您的应用程序从开发到生产只需 2.5 个步骤)，而且是在您自己的服务器上进行。

如果你不想为 Heroku 上的 Dynos 支付昂贵的费用，如果你需要在一个特定的国家托管，或者如果你只是认为用 Heroku 部署太容易了，这是一个很好的解决方案！

## 为什么中国需要多库？

简短的回答是——你不知道。但这使得在中国将应用程序投入生产变得更加简单。Heroku 在中国没有任何服务器，事实上 Heroku 的 SSL 提供商被封锁在长城防火墙下。

Heroku 使用 AWS 服务器来创建它的 Ruby 和 Node.js 构建包(两者都是 Rails 应用程序的依赖项)，它们偶尔也会被阻塞。因此，确保您的应用程序正在运行——并且速度极快——的唯一方法是直接部署在中国的服务器上。

# 开始之前

## 第一步:在阿里云上购买主机

第一步是在 [ECS](https://cn.aliyun.com/product/ecs?spm=5176.12825654.eofdhaal5.2.54212c4aoegNeg) 上购买托管——阿里云的弹性计算服务。Dokku 在 Ubuntu 和 CentOS 上都能很好地工作。老实说，阿里云没有最好的文档，所以对于最初的服务器设置，最好的地方是数字海洋。你可以在这里找到 CentOS 和 Ubuntu 的初始设置说明[，在这里](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7)找到[。](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04)

## 步骤 2:在远程服务器上安装 Dokku 并创建 Rails 应用程序

下一步是在服务器上安装 Dokku 容器。

```
wget [https://raw.githubusercontent.com/dokku/dokku/v0.18.1/bootstrap.sh](https://raw.githubusercontent.com/dokku/dokku/v0.18.1/bootstrap.sh)sudo DOKKU_TAG=v0.18.1 bash bootstrap.sh
```

安装过程大约需要五到十分钟，这取决于您的互联网连接速度。那么，杜库可以走了。

实际创建应用程序大约需要五到六个步骤。关于这个的 [Dokku 文档](http://dokku.viewdocs.io/dokku/deployment/application-deployment/)非常全面，所以我将直接链接到它。

# 如何用 Dokku 和 Docker 部署您的 Rails 应用程序

![](img/33ee092e43eb5541796cfb948209b15e.png)

每个人都可以用 Docker 部署！因为只需要四个步骤。照片由[阿迪·戈尔茨坦](https://unsplash.com/@adigold1)在 [Unsplash](https://unsplash.com/) 上拍摄

## 步骤 1:取消设置代理环境变量

这必须由管理员远程完成。默认情况下，带有 Dokku 文件的 Dokku 部署将监听端口 80，因此无需手动配置。

```
dokku config:unset [APP_NAME] DOKKU_PROXY_PORT_MAP
```

## 步骤 2:向应用程序根添加一个`Dockerfile`。

将 Ruby 和 Bundler 版本更改为您自己的版本，但除此之外，Dockerfile 文件可以按原样使用。Dockerfile 负责获取构建包，即使第一次部署需要将近 15 分钟，所有后续部署都将被缓存，除非添加了新的 gems 或 NPM 包。

```
FROM ruby:2.5.3# Install NodeJS and Yarn
RUN apt-get update
RUN apt-get -y install curl
RUN apt-get install -my gnupg
RUN curl -sL <https://deb.nodesource.com/setup_8.x> | bash -
RUN curl -sS <https://dl.yarnpkg.com/debian/pubkey.gpg> | apt-key add -
RUN echo "deb <https://dl.yarnpkg.com/debian/> stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get -qqyy install nodejs yarn && rm -rf /var/lib/apt/lists/*# Install Ruby Gems and node modules
COPY Gemfile* /tmp/
COPY package.json /tmp/
COPY yarn.lock /tmp/
WORKDIR /tmp
RUN gem install bundler -v 2.0.2
RUN bundle install --jobs 5 --retry 5 --without development test
# RUN yarn install
RUN mkdir /app
WORKDIR /app
COPY . /app
ENV RAILS_ENV production
ENV RACK_ENV production# Execute the Procfile
CMD ["bin/run-dev.sh"]
```

## 步骤 3:使用预部署和后部署脚本将 app.json 添加到根目录。

在这种情况下，我将它设置为在部署前预编译资产，并在部署后自动迁移数据库。

```
{   
  "name": "REPLACE WITH YOUR RAILS APP NAME",   
  "scripts": {     
    "dokku": {       
      "predeploy": "bundle exec rake assets:precompile",     
      "postdeploy": "bundle exec rake db:migrate"     
    }   
  } 
}
```

## 步骤 4:添加密钥库

秘钥库是什么？好问题。密钥用于加密会话，以便浏览器可以安全地向应用程序发送 cookies(即，用于保持用户登录)。

```
# this command will give a secret key. 
# run it in Terminal inside the app folder
rake secretdokku config:set SECRET_KEY_BASE=[add output from the previous command]
```

**执行上述所有步骤一次，然后像往常一样部署到 Dokku。**

```
git push dokku master
```

*感谢您阅读本文！*