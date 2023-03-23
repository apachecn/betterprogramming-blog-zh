# 如何使用 Robo 3T 或其他 MongoDB GUIs 远程连接到 MongoDB

> 原文：<https://betterprogramming.pub/connect-mongodb-database-remotely-using-robo-3t-or-others-mongodb-gui-7a90d5adfa3e>

## 使使用数据库变得更加容易

![](img/1723b3ec019f508bd928db3399e86654.png)

托拜厄斯·菲舍尔在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

我使用 [mLab](https://mlab.com/) 作为开发服务器，所以设置对我来说很容易也很快，可以直接开始开发。一旦我的 webapp 开发完成，是时候在一个真正的服务器上建立一个真正的 [MongoDB](https://www.mongodb.com/) 了。几年前我就这样做了，但那一次，我使用的是数字海洋公司的一键式安装，而这一次我必须在现有的服务器上从头开始。

## 在您的服务器中安装 MongoDB

按照[官方文档](https://docs.mongodb.com/)完成 MongoDB 安装。

## 创建一个数据库和一个用户

使用以下命令打开 Mongo shell:

```
mongo
```

接下来，选择一个数据库:

```
use yourDB 
```

创建一个用户，让他们`readWrite`访问您的数据库。

```
db.createUser({user:’admin’,pwd:’admin’,roles:[{role:’readWrite’,db:’yourDB’}]})
```

## 允许远程访问

默认情况下，MongoDB 不允许远程连接。运行:

```
sudo vim /etc/mongod.conf
```

在`#network interface`中添加您的服务器 IP，如下图:

```
# network interfaces
net:
 port: 27017
 bindIp: 127.0.0.1,your_digitalOcean_droplet_ip_here
```

不要在两个 IP 之间留有空间。

接下来是允许授权。在`#security`，将其更改为:

```
security:
 authorization: enabled
```

现在您可以用您的用户名和密码连接到您的 Mongo GUI。