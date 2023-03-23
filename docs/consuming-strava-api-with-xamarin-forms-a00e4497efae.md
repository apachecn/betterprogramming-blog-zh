# 使用 Xamarin 使用 Strava API。形式

> 原文：<https://betterprogramming.pub/consuming-strava-api-with-xamarin-forms-a00e4497efae>

## 关于如何在 Strava API 和 Xamarin 之间认证、下载和上传活动的指南。表单移动应用程序

![](img/fd2caf81c8ca5d38062ec8210121f31f.png)

由[杰伊·米勒](https://unsplash.com/@therealjaymiller?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/strava?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我一直在做一个移动应用程序，这是一个健身活动跟踪器，这个项目的积压项目之一是与 Strava API 同步。有一些。NET 项目来解决这个问题，但是，它们不符合我的需要。其中一些已经过时，不处理认证过程，或者缺少文档。所以我决定分享我的所作所为，帮助所有经历这些的人。

# 证明

为了允许您的用户使用他们的 Strava 帐户进行连接，认证是您需要做的第一件事。这是通过 OAuth 2.0 实现的，OAuth 2.0 是一个众所周知的用于授权访问应用程序的标准。让我们假设您已经有一个配置了应用程序的 Strava 帐户。这一步我就不赘述了，因为网上有很多素材(查看[此视频](https://www.youtube.com/watch?v=sgscChKfGyg)求助)。授权过程是这样进行的:

1.  该应用程序会打开一个浏览器，导航到一个页面，要求用户输入凭据。
2.  用户输入凭证，并被要求授权应用程序访问他在 Strava 上的数据。
3.  如果成功，浏览器将被重定向到您指定的页面(回拨域)。会有一个名为 code 的 URL 参数。
4.  应用程序检测到重定向，获取代码，浏览器关闭。
5.  应用程序调用 API 并交换代码以访问令牌并刷新令牌。

这里的主要问题是如何打开浏览器并等待回叫(步骤 1)。幸运的是，`Xamarin.Essentials`中的类`WebAuthenticator`可以启动这个流并监听回调([检查这个链接](https://docs.microsoft.com/en-us/xamarin/essentials/web-authenticator?tabs=android)以在项目中设置)。查看以下如何操作:

```
string authURL = 
  "[https://www.strava.com/api/v3/oauth/authorize](https://www.strava.com/api/v3/oauth/authorize)" +
  "?client_id=ReplaceWithClientID" +
  "&redirect_uri=myapp://myapp.com" +
  "&response_type=code" +
  "&approval_prompt=auto" +
  "&scope=activity:read_all,activity:write";WebAuthenticatorResult authResult = 
  await WebAuthenticator.AuthenticateAsync(
    new Uri(authURL), 
    new Uri("myapp://myapp.com"));
```

另一个重要的部分是重定向 URI。在 Strava 帐户中，您将像 myapp.com 一样设置回调域，因此当授权发生时，浏览器将会转向类似于`myapp.com?code=xyz`(步骤 3)的内容。然而，在 app 上，我们需要将重定向 URI 设置为`myapp://myapp.com`，因为在配置`WebAuthenticator`时，我们将使用 url 方案`myapp` ( [本主题](https://groups.google.com/g/strava-api/c/rjId5Ywo6r8/m/-xMeCDCZAwAJ)帮助我解决了这个问题)。

现在你可以从`authResult.Properties[“code”]`那里得到密码，然后进入最后一步，交换密码来访问令牌。此时，您应该熟悉 Postman(或另一个 API 工具)，并且能够测试以下 cURL 请求([查看此处](https://stackoverflow.com/questions/27957943/simulate-a-specific-curl-in-postman)如何在 Postman 上导入它):

```
curl -X POST https://www.strava.com/api/v3/oauth/token \
  -d client_id=ReplaceWithClientID \
  -d client_secret=ReplaceWithClientSecret \
  -d code=ReplaceWithCode \
  -d grant_type=authorization_code
```

成功完成后，响应将包含一些属性，我建议您将这些属性保存在应用程序属性或数据库中。分别是`expires_at`、`access_token`和`refresh_token`。现在，您可以使用 access_token 来请求 API，但是这个令牌很快就会过期(根据 [Strava docs](https://developers.strava.com/docs/authentication/) 的说法，6 个小时之后)。所以如果它仍然有效，就用它。如果没有，使用`refresh_token`获取一个新的。

# 刷新过期令牌

好了，你得到了代码，换成了令牌，现在令牌不再有效了！如果你是第一次这么做，看起来很复杂，但是一旦你有了一个令牌，就只需要知道它是否有效了。如果不是，您必须使用刷新令牌请求一个新令牌:

```
if (tokenExpiresAt > DateTime.Now)
  return savedToken;
else
  // Ask for a new access_token
```

每次需要调用 Strava API 时，都应该检查令牌是否有效，所以我建议创建一个处理这个问题的方法。一旦您获得新令牌，它将伴随着另一个刷新令牌和到期日期。然后覆盖应用程序存储上的旧文件。

# 下载活动

Strava API 有一个[端点，用于列出接受参数“after”的运动员活动](https://developers.strava.com/docs/reference/#api-Activities-getLoggedInAthleteActivities)，以过滤特定时间后发生的活动:

```
"/athlete/activities?after=" + stravaSyncDate
```

下载完活动后，只需将变量更新为实际日期/时间。因此，下一次您将只获得上次同步后的新活动。

您可能还想下载其他活动数据，如心率或距离。这种数据在端点[流](https://developers.strava.com/docs/reference/#api-Streams-getActivityStreams)中可用。这个端点接受一个查询参数`keys`，您可以在其中告知所需的流类型。在我的例子中，我需要心率和时间，所以我向`keys=time`、`heartrate`提出请求。

每个流包含值序列和一个属性`series_type`，这是在流被缩减采样的情况下使用的基本序列(可以是距离或时间)。对于心率，序列类型为时间，因此序列相互匹配:

```
{
    "heartrate": {
        "data": [ 74, 75, 76 ],
        "series_type": "time"
    },
    "time": {
        "data": [ 0, 5, 10 ],
        "series_type": "time"
    }
}
```

# 上传活动

您可能认为要上传活动，只需对上面的端点进行 POST 调用。尽管活动端点允许此操作，但不可能上传流。但是可以向端点`[/uploads](https://developers.strava.com/docs/reference/#api-Uploads-createUpload)`发送数据文件。

他们接受一些格式，我最终选择了基本上是 XML 文件的 GPX。我知道那里有 XML 库，但是在这种情况下，结构非常简单，您可以很容易地创建一个连接一些字符串块的库([查看我在 StackOverflow](https://stackoverflow.com/a/70978548/2083581) 上的建议)。

# 结束

在本文中，我解释了如何使用`Xamarin.Forms`移动应用程序认证、下载和上传活动到 Strava。我试图保持简洁，只显示基本的源代码，但是请查看我的 GitHub 库以获得完整版本。如果有任何疑问，请随时联系我。

[GitHub 上的完整示例](https://github.com/marciosouzajunior/StravaExample)