# 如何让 Angular 和 Nginx 在开发中正常工作

> 原文：<https://betterprogramming.pub/how-to-properly-get-angular-and-nginx-working-together-for-development-3e5d158734bf>

也许你想开始使用虚拟机开发或学习 Angular。也许你已经安装了 Nginx，并想使用 Nginx 为你的 Angular 应用服务。

![](img/ab32e1bd01e4fb433af07564739038b1.png)

徽标版权归谷歌([https://angular.io/](https://angular.io/))和 Nginx([https://www.nginx.com/](https://www.nginx.com/))所有

遵循 [Angular 快速入门指南](https://angular.io/guide/setup-local)创建您的应用`your-app`。以下步骤将允许`ng serve`和`nginx`一起正常工作。

> *按照指南，我把一个* `*nginx.conf*` *文件放在我的项目里面* `*/your-app/config/nginx.conf*` *下面，并把它和* `*/etc/nginx/sites-enabled/your-app.conf*` *链接起来。*

# 务实的方式

起初，你可能会发现自己说，*容易。将您的* `*/dist*` *文件夹作为* `*root*` *放入您的 Nginx 配置中，并使用* `*ng build*` *来更新构建。你现在所要做的就是在浏览器中点击 F5，然后你就可以开始了。*

使用以下配置打开您的应用程序:

```
server {
    listen 80;

    server_name your.app;
    root /vagrant/your.app/dist/your.app location / {
        try_files $uri $uri/ /index.html;
    }
}
```

在对源代码进行修改后，您必须再次运行`ng build`。如果您的源代码发生变化，您可能希望使用`--watch`参数来允许构建重新运行。在您的主机上调用`http://your.app`工作正常。

使用虚拟机时，您可能会发现更新 hosts 文件以将`your.app`映射到`localhost`会很有帮助。

# 更好的方法

有一个更好的方法可以让你的浏览器在你修改后自动重新加载:`ng serve`将运行一个本地服务器(默认为`http:/localhost:4200`)并将 Angular 编译后的源代码直接提供给你的浏览器。

Nginx 配置与上面的类似；但是，你必须做出调整。您需要使用`proxy_pass`来实际调用`localhost:4200`上的内部运行节点服务器。

```
server {
    listen 80;

    server_name your-app;

    location / {
        proxy_pass       http://localhost:4200;
        proxy_set_header Host            $host;
    }
}
```

确保使用`--public-host http://your.app`参数运行`ng serve`，否则应用程序将拒绝启动。

重启`nginx`后，在你的主机上打开浏览器，你的应用应该可以看到了。

# WebSocket 问题

打开控制台后，您可能会看到如下错误:

> *WebSocket 连接到“ws://your-app/sock js-node/884/1u 4 qfrnl/WebSocket”失败:web socket 握手期间出错:意外响应代码:400*

这是因为 [WebSockets](https://en.wikipedia.org/wiki/WebSocket) 使用了两个 HTTP 头，即`Connection`和`Upgrade`头。在将`nginx`配置为使用`proxy_pass`之后，您需要传递额外的头。

```
server {
    listen 80;

    server_name your.app;

    location / {
        proxy_pass       http://localhost:4200;
        **proxy_set_header Upgrade    $http_upgrade;**
        **proxy_set_header Connection $http_connection;**
        proxy_set_header Host       $host;
    }
}
```

在此之后，您的控制台应该没有错误，并且在对源代码进行更改之后，浏览器应该会自动重新加载。