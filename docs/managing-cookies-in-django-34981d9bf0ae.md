# 在 Django 中管理 Cookies

> 原文：<https://betterprogramming.pub/managing-cookies-in-django-34981d9bf0ae>

## 通过示例了解 Django cookies

![](img/4fdbb62b860e074225ef7caafdac2b41.png)

约翰·黄在 [Unsplash](https://unsplash.com/s/photos/jar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

HTTP 是一种无状态协议，因此当请求发送到服务器时，它不知道您是第一次请求该页面，还是您是以前多次访问过该页面的同一用户。

这种 HTTP 无状态对于电子商务网站开发人员来说是一个问题，因为请求之间的持久性可以用来推荐产品或在购物车中显示产品。为了满足这种需要，cookie 应运而生。

cookie 是存储在用户浏览器中的一小段数据，由服务器发送。它们通常用于存储用户偏好。

一般来说，cookies 是这样工作的:

1.  浏览器向服务器发送请求。
2.  服务器将响应连同一个或多个 cookies 一起发送给浏览器。
3.  浏览器保存它从服务器收到的 cookie。从现在开始，每次向服务器发出请求时，浏览器都会将这个 cookie 发送到服务器，直到 cookie 过期。
4.  当 cookie 过期时，它将从浏览器中删除。

# 使用 Cookies

[Django](https://www.djangoproject.com/) `HttpResponse`对象有一个`set_cookie()`方法。

语法为:

```
set_cookie(*key*, *value=''*, *max_age=None*, *expires=None*, *path='/'*, *domain=None*, *secure=None*, *httponly=False*, *samesite=None*) :
```

1.  `name`:曲奇的名字。
2.  `value`:要存储在 cookie 中的值。您可以设置 int 或 string，但它将返回 string。
3.  `max_age`:应该是秒数，或者`None`(默认)，如果 cookie 应该只持续客户端的浏览器会话。如果没有指定`expires`，将被计算。
4.  `expires`:应该是格式为`"Wdy, DD-Mon-YY HH:MM:SS GMT"`的字符串或者是 UTC 格式的`datetime.datetime`对象。如果`expires`是`datetime`对象，则`max_age`将被计算。

检查 [Django 文档](https://docs.djangoproject.com/en/2.2/ref/request-response/#django.http.HttpResponse.set_cookie)中的完整方法定义。

每个 Django `request`对象都有一个`COOKIES`属性，这是一个字典。我们可以使用`COOKIES` 来读取一个 cookie 值，如下所示，即使你设置了一个整数值，它也返回一个字符串:

```
request.COOKIES[‘cookie_name’]
```

我们举个例子。

在您的`views.py`中创建一个视图，如下所示:

```
def test_cookie(request):   
    if not request.COOKIES.get('team'):
        response = HttpResponse("Visiting for the first time.")
        response.set_cookie('team', 'barcelona')
        return response
    else:
        return HttpResponse("Your favorite team is {}".format(request.COOKIES['team']))
```

现在，在`urls.py`中添加这个视图的 URL。

```
urlpatterns = [
    path('test_cookie/', views.test_cookie, name='test_cookie'),
]
```

当您第一次浏览`http://127.0.0.1:8000/test_cookie/`时，它会发送 cookie `team`和响应，浏览器会存储它。这里，我们没有设置`max_age`，所以当浏览器关闭时，浏览器将删除 cookie。

当用`test_cookie`或任何其他请求在浏览器中设置 cookie 时，对`http://127.0.0.1:8000/test_cookie/`(或`http://127.0.0.1:8000/`的任何其他页面)的每个后续请求将把所有 cookie 发送到服务器。

`HttpResponse`不包含任何 cookies，除非您包含`set_cookie()`。

要在 Google Chrome 中查看服务器发送的 cookies，请按 CTRL+Shift+J，这将打开开发者控制台。

## 删除或更新 cookie

要获得 cookie，只需调用`response.delete_cookie(‘cookie_name’)`。`HttpResponse`中没有 cookie 更新方法，使用`set_cookie()`更新 cookie 值或到期时间。

## 使用 cookies 时请记住这一点

1.  永远不要使用 cookies 来存储密码等敏感数据。Cookies 以纯文本形式存储数据，因此任何人都可以读取/修改它们。
2.  大多数浏览器不允许 cookie 存储超过 4KB 的数据(即每个 cookie 存储 4KB)。此外，大多数浏览器接受每个网站不超过 30 个 cookies。实际上，每个网站的确切 Cookie 数量因浏览器而异，更多详情请访问 [*浏览器 Cookie 限制*](http://browsercookielimits.squawky.net/) 。
3.  回想一下，一旦在浏览器中设置了 cookie，它就会随每个请求一起发送到服务器。假设我们添加了 20 个大小为 4KB 的 cookies，总计 80KB。这意味着，对于服务器的每个请求，浏览器都需要发送 80KB 的额外数据！
4.  用户可以随意删除 cookies。用户甚至可以配置他们的浏览器完全不接受 cookies。