# Docker 内部:一个 Nginx，但是根据你的主机名有不同的 PHP 版本

> 原文：<https://betterprogramming.pub/inside-docker-one-nginx-but-different-php-versions-based-on-your-hostname-2d4aca6654bd>

## nginx http map 模块的实际用例

![](img/a40658235f23a95451d52cebd4823a12.png)

乔恩·泰森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

好吧，这里有一个每个人都应该知道的日常场景:

您有一个典型的 docker-compose.yml 设置

*   nginx
*   php-fpm (PHP 7)
*   数据库(这里对我们不重要)

假设您当前的 PHP 版本是 7.4，您想将您的应用程序升级到 PHP 8.1，只需扩展 docker-compose.yml，而不是只进行“复制粘贴”和复制所有内容。目标是保持两个版本同时运行，而不是替换现有的配置。

有很多方法可以做到这一点，而且都可能没问题。在这篇文章中，我只是想向你展示一条我多年后都不知道的路。

所以你的默认设置可能完全不同，所以我想向你展示一下我的 PHP 设置:

没什么特别的，就是简单的`nginx` / `php-fpm`连击。
`host.conf`:

```
server {
    listen 80;
    server_name myapp.com;
    root /app/public;
    include /etc/nginx/php.conf;
}
```

`php.conf` (只是重要的部分):

```
location ~ \.php$ {
    try_files $uri =404;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass php-fpm7:9000;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param PATH_INFO $fastcgi_path_info;
}
```

到目前为止，一切顺利，没什么特别的。假设我们想要添加新的应用程序，保留所有配置文件并减少重复。我无意中发现了`nginx`的一个很好的特征:`[ngx_http_map_module](http://nginx.org/en/docs/http/ngx_http_map_module.html)`——这么多年过去了，我一直不知道它的存在——直到现在:)

让我向您展示一下在这种特殊情况下我是如何使用该模块的:

在“http”部分的`nginx.conf`中，我添加了以下内容:

```
map $http_host $phpfpm {
 'myapp.com'         'php-fpm7'; <--- /app
 'new.myapp.com'     'php-fpm8'; <--- /app-new
}
```

我用新的虚拟主机扩展了我的“host.conf ”:

```
server {
    listen 80;
    server_name new.myapp.com
    root /app-new/public; <----- different folder !
    include /etc/nginx/php.conf; <--- same config
}
```

我们记得，在我们的`php.conf`中，我们有一个固定的“fastcgi_pass”:

```
php-fpm7:9000
```

因为有了新的映射，我们现在可以用新的$变量替换它，所以这一行改为:

```
$phpfpm:9000
```

但是——这也是我必须学习的东西——因为 nginx 运行在 Docker 中，我们必须告诉 nginx，nginx 应该使用哪个解析器才能从我们的映射主机名中获得正确的 IP:`php-fpm7`和`php-fpm8`，这就是为什么我们必须添加这个:

```
resolver 127.0.0.11 ipv6=off;
```

这是我们`php.conf`的最终结果:

```
location ~ \.php$ {
    resolver 127.0.0.11 ipv6=off;
    try_files $uri =404;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass $phpfpm:9000;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param PATH_INFO $fastcgi_path_info;
}
```

有了这个设置，nginx 就可以根据我们的域名知道我们想要使用哪个 PHP-Container，cool)

当然，我们必须扩展我们的 docker-compose.yml:

在“nginx”服务中，我们必须安装您的“app-new”目录:

```
- /home/app-new:/app-new
```

我们添加另一个具有相同映射的 PHP 服务，并将其命名为`php-fpm8`。

最终结果如下所示:

同样，许多解决方案会得出相同的结果，但我个人想看看这种“映射”是如何工作的，以及如何使用它，我对这种解决方案很满意，因为:

*   我可以重用我现有的主配置:`php.conf`
*   我可以并行使用不同的 PHP 版本，同一个应用程序或我的应用程序的另一个版本
*   我“可以”在每个项目中使用我的`php.conf`,这不是最好的主意，但是可能的，因为现在“映射”告诉 nginx 使用哪个容器

当然，如果你想同时用不同的 PHP 版本测试你的应用程序，你的映射应该是这样的:

```
map $http_host $phpfpm {
 'myapp.com'         'php-fpm5';
 'php7.myapp.com'    'php-fpm7';
 'php80.myapp.com'   'php-fpm80';
 'php81.myapp.com'   'php-fpm81';
}
```

这只是一个例子，你得到的想法；)

对我来说，这是一种在同一个应用程序中处理不同 PHP 版本的好方法。

**提示:**将映射放入一个单独的配置中，在我们的示例中，让我们创建一个
a `php_mapping.conf`并将映射放入其中(不要忘记在您的 nginx 服务中挂载它)并将我们的`nginx.conf`的“http”部分中的映射替换为:

```
include /etc/nginx/php_mapping.conf;
```

这样，甚至我们的`nginx.conf`也可以在其他项目中重用！

一如既往，我希望你能找到任何有用的信息