# 在 Django 管理会话

> 原文：<https://betterprogramming.pub/managing-sessions-in-django-92ef72db4c63>

## 通过示例了解您的 Django 会话

![](img/59b70629aed5ed42320d1fe97543108f.png)

[米米·蒂安](https://unsplash.com/@mimithian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/session?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

在我的文章“[在 Django](https://medium.com/@goutomroy/managing-cookies-in-django-34981d9bf0ae) 中管理 cookie”中，我讨论了 cookie 如何让我们轻松地在浏览器中存储数据。尽管毫无疑问 cookies 是有用的，但它们有以下问题:

1.  攻击者可以修改 cookie 的内容，这可能会破坏您的应用程序
2.  我们不能存储敏感数据
3.  我们只能在 cookies 中存储有限的数据。大多数浏览器不允许 cookie 存储超过 4 Kb 的数据。将数据分成多个 cookies 会导致每个请求的开销过大。此外，你甚至不能依赖每个域的浏览器所允许的 cookies 数量。

我们可以通过会话轻松解决这些问题。

## 会话是这样工作的:

当我们使用会话时，数据并不直接存储在浏览器中。相反，它存储在服务器中。

Django 创建一个惟一的 32 个字符长的随机字符串，称为会话密钥，并将其与会话数据相关联。

然后，服务器向浏览器发送一个名为`sessionid`的 cookie，其中包含会话密钥作为值。

在后续请求中，浏览器将 cookie `sessionid`发送给服务器。Django 然后使用这个 cookie 来检索会话数据，并使它在您的代码中可以访问。

# 会话配置

为了在 Django 中建立一个会话，我们需要在我们的`settings.py`中添加两件事情:

*   `‘django.contrib.sessions.middleware.SessionMiddleware'`到`MIDDLEWARE`
*   `'django.contrib.sessions'`至`INSTALLED_APPS`。运行`python manage.py migrate`来填充表格。该表有三列:`session_key`、`session_data`和`expire_date`。

当我们用`startproject`命令创建一个新项目时，它们会被自动添加。

# 测试浏览器的 Cookie 保存能力

我们知道用户可以配置他们的浏览器不接受任何 cookie。因此，Django 提供了一些方便的方法来检查浏览器中的 cookies 支持。`request.sessions`对象提供了以下三种方法来检查浏览器中的 cookies 支持。

`set_test_cookie()`:设置一个测试 cookie，判断用户的浏览器是否支持 cookie。由于 cookies 的工作方式，在用户的下一个页面请求之前，您无法测试这一点。

`test_cookie_worked()`:根据用户的浏览器是否接受测试 cookie，返回`True`或`False`。由于 cookies 的工作方式，你必须在之前单独的页面请求中调用`set_test_cookie()`。

`delete_test_cookie()`:删除测试 cookie。用这个清理你自己。

我们来讨论一个例子。

```
def e_commerce_home(request): if request.session.test_cookie_worked():
        request.session.delete_test_cookie()
    else:
        request.session.set_test_cookie()
        messages.error(request, 'Please enable cookie') return render(request, 'home/home.html')
```

当用户浏览这个页面时，它会检查浏览器中是否启用了 cookie。如果没有，那么它会发送一个测试 cookie 并显示一条错误消息，以便在浏览器中启用 cookie。如果浏览器接受 cookie，那么它将删除先前发送的测试 cookie。

# 读取和写入会话数据

Django `request`对象有一个`session`属性，其作用类似于一个字典。

## #设置会话数据

```
request.session[‘user_id’] = ‘20’
request.session[‘team’] = ‘Barcelona’
```

## #读取会话数据

```
request.session.get(‘user_id’) # returns ‘20’
request.session.get(‘team’) # returns ‘Barcelona’
```

## ##删除会话数据

```
del request.session[‘user_id’]
del request.session[‘user_id’]
```

# SessionMiddleware 的角色

让我们检查一下`‘django.contrib.sessions.middleware.SessionMiddleware'`代码，了解一下它是做什么的。

*   中间件在视图被调用之前和之后被调用。`process_request(self, request)`是之前消耗的，`process_response(self, request, response)`是视图调用后消耗的。
*   `process_request`检查`request.COOKIES`中是否有名为`sessionid(default value for settings.SESSION_COOKIE_NAME)`的 cookie。如果找到，它会尝试使用会话数据库中的`session_key`列填充会话。
*   `process_response`检查`request.session`是否被修改或创建。然后，它创建会话数据或将会话数据保存到会话数据库，并使用`session_key value`将一个`sessionid` cookie 添加到响应中。如果它发现`request.COOKIES`不为空，并且所有会话数据都被删除，那么它从数据库中删除会话行，并从响应中删除 cookie，这也删除了浏览器中的 cookie。

让我们创建几个视图来清楚地理解它。

```
def **save_session_data**(request):
    # set new data
    request.session['user_id'] = 20
    request.session['team'] = 'Barcelona'
    return HttpResponse("Session Data Saved")def **access_session_data**(request):
    response = ""
    if request.session.get('user_id'):
        user_id = request.session.get('user_id')
        response += "User Id : {0} <br>".format(user_id)
    if request.session.get('team'):
        team = request.session.get('team')
        response += "Team : {0} <br>".format(team)

    if not response:
        return HttpResponse("No session data")
    else:
        return HttpResponse(response)def **delete_session_data**(request):
    try:
        del request.session['user_id']
        del request.session['team']
    except KeyError:
        pass

    return HttpResponse("Session Data cleared")
```

下面是调用`save_session_data()`视图时发生的情况。

1.  `'django.contrib.sessions.middleware.SessionMiddleware'`中间件创建一个新的随机会话密钥，并将会话数据与其相关联。
2.  `'django.contrib.sessions.middleware.SessionMiddleware'`使用`'django.contrib.sessions'`应用程序将会话数据存储在数据库中。
3.  最后，在步骤 1 中生成的带有随机值(会话密钥)的名为`sessionid`的 cookie 被发送到浏览器。
4.  从现在开始，浏览器将把这个`sessionid` cookie 和每个请求一起发送到服务器，允许 Python 代码使用`request.session`访问视图中的会话数据。

在`save_session_data`之后调用`access_session_data`时，`sessionid` cookie 在`request.COOKIES`中可用。中间件的`process_request`使用这个`sessionid`来填充这个请求的会话。

在`delete_session_data`中，我们将删除所有会话数据。中间件识别出这个会话没有会话数据，所以它删除了会话行，也删除了响应 cookie，从而删除了浏览器中的 cookie。

# 保存会话时

默认情况下，Django 仅在会话被修改时保存到会话数据库，也就是说，如果它的任何字典值被分配或删除:

```
*# Session is modified.*
request.session['foo'] = 'bar'

*# Session is modified.*
**del** request.session['foo']

*# Session is modified.*
request.session['foo'] = {}
```

这里值得一提的另一个要点是，Django 只在会话数据被修改时才向浏览器发送会话 cookie。在此过程中，它还会更新 cookie 的到期时间。

会话数据库有三列:

`session_key`:存储唯一随机会话 ID(或 SID)

`session_data` : Django 以编码格式存储会话数据。要获得原始数据，使用会话对象的`get_decoded()`方法。

`expire_date`:会话 cookie 的到期日期