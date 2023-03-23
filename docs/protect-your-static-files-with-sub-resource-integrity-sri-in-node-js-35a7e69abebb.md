# 使用 Node.js 中的子资源完整性(SRI)保护静态文件

> 原文：<https://betterprogramming.pub/protect-your-static-files-with-sub-resource-integrity-sri-in-node-js-35a7e69abebb>

## 如何执行自动 SRI 生成，以递归地生成子资源完整性哈希来进行身份验证。js 和。css 文件

```
**Recommendations:** Compatibility test performed with Node.JS 18.10, executed on MacOS Ventura 13.1.Libraries Used: fs, path, crypto, Express.js, (optional: Mongoose)
```

子资源完整性(SRI)是一项安全功能，它允许浏览器验证它们获取的文件(例如，从 CDN 获取的文件)是在没有意外操作的情况下交付的。要使用 SRI，希望包含静态资源的网站作者除了指定资源的位置之外，还可以指定资源的加密哈希。然后，获取资源的浏览器可以将网站作者提供的哈希与从资源计算出的哈希进行比较。如果哈希值不匹配，资源将被丢弃。

integrity 属性的值以至少一个字符串开始，每个字符串包括指示特定哈希算法的前缀(当前允许的前缀是 sha256、sha384 和 sha512)，后跟破折号，并以 base64 哈希本身结束。

```
<script src="https://cdn.example.com/app.js"
        integrity="sha384-+/M6kredJcxdsqkczBUjMLvqyHb1K/JThDXWsBVxMEeZHEaMKEOEct339VItX1zB"
></script>
```

本实践教程旨在通过解析目录和子目录来提取静态文件，从而自动化生成加密哈希的过程，以方便对它们的访问。

![](img/5282c43c00da45bf7f276d6a696f6a26.png)

美国宇航局在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 实践教程

该算法的核心是递归分析包含站点静态文件的“公共”路径的子文件夹，对每个子文件夹进行单独处理。js 或者。css 文件并浏览到每个子文件夹。

请注意，递归函数封装在“get”函数中，旨在容纳各种参数，以便作为模块进行有用的导出，并在 [Express](https://www.npmjs.com/package/express) 应用程序中正确使用。

`get`函数有两个参数:包含我们想要从中获取散列的静态文件的`directory` ( `string`)，以及计算所需的`algorithm``sha256`、`sha384`或`sha512` ( `string`)。返回`JSON`对象。

但是，我们希望确保只为。css 和。js 文件，不包括系统文件、图像和其他文件。然后，创建一个控制函数来分析各种文件的扩展名，以在条件无效的情况下拒绝算法的执行，这就足够了。

因此，在条件验证的框架内，算法将使用加密模块和 fs 模块来连续访问要研究的文件，并创建关于其内容和由开发者确定的算法的打印。

这将返回如下内容:

```
[
  {
    filepath: 'public/css/index.css',
    hash: 'sha384-yTRuc6ItZNScTYLXkpwURSAsaZLKgfoWi69Nku5bK2/1HW2O8OOgcli2jdgvIJnE'
  }
]
```

然后，我们可以观察到一个 JSON 元素的创建，该元素包含目录及其子目录中包含的每个文件路径，以及一个惟一散列的赋值，该散列前面带有可以在 routes 配置文件中直接利用的`"sha256-"`、`"sha384-"`或`"sha512-"`。

[](https://github.com/louisbrulenaudet/open-sri) [## GitHub——louisbrulenaudet/open-Sri:超轻、不依赖和极简的开源包…

### 超轻、不依赖和极简的开源包，可递归生成 sha-256、sha-384 或 sha-512…

github.com](https://github.com/louisbrulenaudet/open-sri) 

# MongoDB 集成

从使用 MongoDB 和 mongose 的更加结构化的集成的角度来看，应用相同的处理就足够了，包括管理 mongose 模式。

如果这是一个全新的项目，确保先用`npm init`命令创建一个`package.json`，安装 [MongoDB](https://www.mongodb.org/downloads) 和 Mongoose。

## 正在连接到 MongoDB

首先，我们需要定义一个连接。如果你的应用只使用一个数据库，你应该使用`mongoose.connect` ( `createConnection`取一个`mongodb://` URI，或者参数`host, database, port, options`)。

**注意:** *如果本地连接失败，尝试使用 127.0.0.1，而不是 localhost。当本地主机名更改时，有时可能会出现问题。*

**重要提示:**mongose 会缓冲所有命令，直到它连接到数据库。这意味着您不必等到它连接到 MongoDB 才定义模型、运行查询等。

# 定义哈希存储模型

通过`Schema`接口定义模型。我们需要每个文件的路径和散列之间的配对系统，在模式中定义如下:

对于本教程，我们做了一些修改，将整个算法集中在一段简短易懂的代码中，允许进行大量的调整和修改:

一旦我们通过`mongoose.model('integrity', IntegritySchema)`定义了一个模型，我们就可以通过下面的函数来使用它:

第一个参数是描述 Mongoose 模式的文件的路径，第二个参数是包含一组子文件夹和静态文件的路径，我们希望将子资源完整性签入的散列收集到这些文件中。

执行`saveSubresourceIntegrityHash`功能时，可以在数据库中观察到目录及其子目录中包含的每个文件的对象创建。在绝对访问路径相同的情况下，我们还可以看到它的替换，以及前面带有“sha384-”的唯一散列的分配，如下所示:

```
{
  "_id": {
    "$oid": "6308ca92d8424fda52e63eae"
  },
  "filepath": "public/css/index.css",
  "__v": 0,
  "hash": "sha384-ql4jKwyXlyERdFY59Fq4T+ILy2bHHIusVmm1UBuSl+ddrgTfN/kAlG7lbY2oo645"
}
```

[](https://github.com/louisbrulenaudet/mongoose-subresource-integrity) [## GitHub-louisbrulenaudet/mongose-sub resource-integrity:超轻、非依赖性和极简主义…

### 超轻，不依赖和极简的开源包递归生成 sha-384 子资源完整性…

github.com](https://github.com/louisbrulenaudet/mongoose-subresource-integrity) 

使用 CDN 托管多个站点之间共享的脚本和样式表等文件可以提高站点性能并节省带宽。然而，使用 CDN 也有风险——如果攻击者控制了 CDN，他或她可以将恶意内容注入到文件中(或完全替换它们),因此也可能攻击从该 CDN 检索文件的所有站点。

> 子资源完整性检查允许您降低这种攻击的风险，方法是确保您的应用程序或 web 文档使用的文件(来自 CDN 或其他地方)在交付时没有任何第三方更改，没有将附加内容注入到文件中，也没有对这些文件进行任何其他类型的更改。

感谢您的阅读。