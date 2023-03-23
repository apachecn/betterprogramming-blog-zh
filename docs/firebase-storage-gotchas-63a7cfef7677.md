# Firebase 存储陷阱

> 原文：<https://betterprogramming.pub/firebase-storage-gotchas-63a7cfef7677>

## 我在实施私有资产和恢复两天工作时学到的 Google 云存储要点

![](img/9687d8a7df0fe1602b28f1cb89666d09.png)

照片由 [Element5 数码](https://unsplash.com/@element5digital?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

你上一次恢复几个工作日是什么时候？

最近，我花了一些时间来为我们的 web 演示编辑器 [DeckDeckGo](https://deckdeckgo.com) 的每个用户创建私有资产和内容。

两个工作日之后，我终于注意到我误解了 [Google 云存储](https://firebase.google.com/docs/storage)的一个基本点，我发现我必须恢复我的新实现，因为我们的资产实际上已经是私有的了。

这就是我写这篇新博客的原因，希望我的“不幸”经历可以在未来帮助其他人。

# 规则:存储没有访问 Firestore 的权限

有时候，当事情没有被写下来，我会问自己，是因为它们不可能还是因为它们实际上是可能的。

能够通过查询 [Firestore](https://firebase.google.com/docs/firestore) 来编写存储规则就是其中之一，答案是*否*。目前无法从其他产品访问 Firebase 产品。

# 规则:用户读写权限

可以将对存储器的访问、读取和写入限制为仅授权用户。

但是上述规则仍然意味着用户可以覆盖其他用户提供的数据。为了克服这个问题，我们可以在存储中的每个用户的数据前加上他们各自的`userId`。

例如，如果您使用的是 [Firebase JavaScript SDK](https://github.com/firebase/firebase-js-sdk) ，上传将如下所示:

```
const ref: Reference =
      firebase.storage().ref(`${userId}/assets/photo.jpg`);

await ref.put(data);
```

一旦存储文件在该结构中排序，我们就能够定义如下规则，该规则仅允许用户在各自的存储文件夹中写入和读取数据:

# 格式:存储参考

要使用授予的规则访问权限访问公共文件或私有文件，存储 URL 可以由以下部分组成:

```
<img src={`https://firebasestorage.googleapis.com/v0/b/${projectId}.appspot.com/o/${encodeURIComponent(path)}?alt=media`}/>
```

其中,`${projectId}`是 Firebase 项目的 ID,`${path}`是存储中文件的路径(一个字符串),必须用`[encodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)`进行编码才能兼容。

# 获取:使用 OAuth 2 安全地访问图像

如果我们定义上述规则或任何其他仅向经过身份验证的用户授予对存储的访问权限的规则，那么就有可能使用 JavaScript 通过 [OAuth 2](https://oauth.net/2/) 获取和加载图像，正如 Bryan Burman 在他的博客文章 [*如何使用 OAuth 2.0*](https://www.twelve21.io/how-to-access-images-securely-with-oauth-2-0/) 中所显示的那样。

# downloadURL:公共但私有

这是我最大的“陷阱”,也是我恢复工作时间的原因。

无论您是否使用，Firebase 都会为上传到存储器中的每一项资产创建一个`downloadUrl`,它是公开的，不受您的规则限制，并且可以在互联网上访问。

但是，因为该网址包含一个任何人都不可能猜到的`token`，只要我们不与任何人分享，这些网址就是私有的。

例如，这里的`downloadUrl`使用令牌作为参数来授予对文件的访问权限。

```
<img src={`https://firebasestorage.googleapis.com/v0/b/${projectId}.appspot.com/o/${path}?alt=media&token=4733325a-78ff-444d-a67c-01fd8ab30fe`}/>
```

在这个过程中，这是我的一个转折点，我必须感谢道格·史蒂文森回答了我关于堆栈溢出的问题。

请注意，在没有绝对把握的情况下，我认为如果您从服务器端使用云解决方案来指示 Firebase 不生成这样的 URL 是可能的，但从 web/客户端肯定是不可能的。

# downloadUrl:生命周期

Firebase 存储令牌不会过期(参见[堆栈溢出](https://stackoverflow.com/a/42598354))。

因此，在没有任何其他修改的情况下，我们的`downloadUrl`也永远不会过期并保持可用。

但是，有没有可能在 Firebase 控制台中使一个特定的 URL 无效。然而，一旦失效，Firebase 将为选中的文件分别创建一个新的`downloadUrl`和一个新的`token`。

同样值得注意的是，对于每次覆盖，Firebase 还会生成一个新的`token`。这意味着，例如，如果我们的应用程序的用户能够上传文件，那么每当他们再次上传文件而不改变其名称时，就会自动创建一个新的`downloadUrl`。

# 结论

就是这样，我想这就是我学习的总结。Firebase Storage 绝对是一款不可思议的、对开发者友好的软件，并且 [DeckDeckGo](https://deckdeckgo.com) 的每个用户的所有内容和资产都是私有的，直到他们决定公开分享他们的演示。

到无限和更远的地方。

大卫