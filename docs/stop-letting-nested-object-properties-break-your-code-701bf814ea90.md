# 不要让嵌套的对象属性破坏您的代码

> 原文：<https://betterprogramming.pub/stop-letting-nested-object-properties-break-your-code-701bf814ea90>

## 使用可选的链接运算符(`?.`)帮助处理未定义的父属性

![](img/d1400f002ac0ec44cd273a189261b11b.png)

[帕特里克·鲍姆](https://unsplash.com/@gecko81de?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/structure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果您处理的是非结构化的、不可预测的数据，那么我相信您以前也见过这个错误:

```
TypeError: Cannot read property ‘hello’ of undefined
```

不可避免地，有人会发送深度嵌套属性的父属性未定义的数据。现在，我假设您已经解决了这个潜在的错误——我知道我已经解决了——但是它有点笨拙，可能看起来像这样:

```
if(record.education && record.education.bachelors) {
   // do stuff with record.education.bachelors
}
```

我在这里做的是检查以确保在 parent(我的顶层变量/对象名)中定义了属性`child`。然后，我检查`grandchild`是否被定义，这样我就可以用`greatgrandchild`做一些事情。

对我来说，我处理的数据有很多很多层次，这种检查最多是碍眼，最糟糕的是搞砸了。

还好有解决的办法！

# 可选的链接运算符

如果我们希望值返回`undefined`，而不是抛出`TypeError`，那么我们可以用可选的链接操作符`?.`替换`.`。

本质上，可选的链接操作符所做的是，如果在链的任何地方有一个断点——未定义或空属性——就返回`undefined`。

```
const record = {
  "first_name": "Jonathan",
  "last_name": "Hsu",
  "education": null
};console.log(record.education.bachelors)   // TypeError
console.log(record?.education?.bachelors) // undefined
```

这使您能够消除对每个嵌套属性的逐步检查。当检查一个属性时，这可以节省很多额外的代码。

# 有效性

这个新操作符是 ECMAScript 2020 规范的一部分。有关全部细节，请参考他们的[规格文件](https://tc39.es/ecma262/)。