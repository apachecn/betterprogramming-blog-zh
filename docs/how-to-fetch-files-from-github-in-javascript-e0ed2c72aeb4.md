# 如何用 JavaScript 从 GitHub 获取文件

> 原文：<https://betterprogramming.pub/how-to-fetch-files-from-github-in-javascript-e0ed2c72aeb4>

## 使用 GitHub API 克服 CORS

![](img/da1416b486891f16f50e26bfd39fb4d7.png)

照片由[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 背景

我们经常希望在 web 应用程序中使用来自 GitHub 的公共托管文件。这可以是任何东西，从小的数据或配置文件，到用于测试的几行代码。虽然我们可以使用`curl`或`wget`等工具在本地下载文件的副本，但由于不匹配[跨源资源共享(CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 身份验证，浏览器不会出现这种情况。

正是因为这个原因，我们利用了 GitHub API。

# 收集有关文件的信息

我们首先获取关于我们感兴趣的回购和文件的信息。例如，如果我们对中型 API 存储库自述文件感兴趣，我们可以从 URL 开始:

```
[https://github.com/**Medium**/**medium-api-docs**/blob/master/**README.md**](https://github.com/Medium/medium-api-docs/blob/master/README.md)
```

然后，我们需要提取三条关键信息:

*   地主
*   仓库
*   我们感兴趣的文件的相对路径

在这种情况下，所有者是`Medium`，存储库是`medium-api-docs`，文件路径是`README.md`。

# 获取文件 SHA 密钥

现在我们有了信息，我们可以将这些变量代入 API 调用:

```
https://api.github.com/repos/**<owner>**/**<repo>**/contents/**<path>**
```

这个函数将返回一个 JSON 对象，包含关于我们的文件和存储库的信息。我们最感兴趣的是文件的 **SHA-1 散列**指纹。

GitHub 将存储库文件存储为 blobs(二进制大对象),我们将使用 SHA 密钥来访问其中的数据。

# 提取斑点

现在我们有了`sha`键，我们向 GitHub API 发送另一个调用，请求 blob 内容。

```
https://api.github.com/repos/**<user>**/**<repo>**/git/blobs/**<sha>**
```

这会返回一个包含内容字段的 JSON 对象。内容是 base64 编码的，需要用 JavaScript `atob`函数解码。

```
blob = fetch(bloburl).then (d => d.json ())
```

如果我们正在读取一个 JSON 文件，我们也可以使用`JSON.parse`将其转换成脚本中的一个对象。

```
data = blob.then (d => JSON.parse (atob (d.content)));
```

# 把所有的放在一起

最后，随着时间的推移，我们很可能需要使用这段代码，我们可以将它转换成一个函数，并用我们的库加载它。下面显示了一个示例版本。

这就是你的 GitHub JSON 文件，它是你前端代码的一部分。

感谢阅读。