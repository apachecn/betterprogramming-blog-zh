# 用 OpenAI 和 GPT-3 构建一个垃圾邮件检查器

> 原文：<https://betterprogramming.pub/build-a-spam-checker-with-openai-and-gpt-3-8d12188780a2>

## 使用微调的 API 创建垃圾邮件过滤器的简单教程

![](img/cc84d1b14e9217c8fdeacbff1bc4d012.png)

缺省情况下，OpenAI API 会给你不同的 AI 模型或引擎来适应不同的情况。

[微调](https://beta.openai.com/docs/guides/fine-tuning)功能允许采用 OpenAI 的模型/引擎，为它们提供新的训练数据，并构建一个新的“微调”模型
我们将使用这一微调功能来构建我们的垃圾邮件检查器。

但是首先，为了建立我们的微调模型，我们需要一些训练数据。
在[呼叫助手](https://www.callassistant.ai)中，我们可以使用用户筛选过的现有机器人呼叫的数据。

我们需要电话推销员和机器人电话以及合法电话的例子，以便该模型可以更好地将文本分类为垃圾邮件

下面是一个电话推销员试图向用户推销某种信贷解决方案的例子:

> 你好，我是信用专家莎拉。我给你打了几次电话，都没有接通。我们已经帮助成千上万的人提高了他们的信用，我们也很乐意帮助你。所以尽快用这个号码给我回电话。期待聊天。谢了。

这里有一个合法看涨期权的例子

> 嗨，我是儿童牙医波特医生办公室的斯潘塞，是关于史密斯先生的。他下个月会去做卫生检查。你想预约吗？拜托了。请致电 XXX–XXX–XXX 联系我们。再说一遍，我们的电话号码是 xxx-xxx-xxx。祝你有美好的一天。再见。

我们需要在提示和结果之间添加一个分隔符。对于这个例子，我们将使用 OpenAI 在其教程中建议的`\n\n###\n\n`。使用这些数据，我们需要上传一个`JSONL`文件，按照要求格式化数据。这是一个例子。你应该尽可能多地增加例子。

编译完文件后，我们需要开始微调过程。你需要大量的数据、OpenAI CLI 和 API 密匙

```
openai api -k sk-YOUR_KEY fine_tunes.create -t file.jsonl -m ada
```

根据模型和训练数据量的不同，完成微调可能需要一些时间。要跟踪进度，您可以使用以下命令:

```
openai api -k sk-YOUR_KEY fine_tunes.follow -i ft-aBcDeFgHiJkLmNoP
...
[18:26:40] Fine-tune enqueued. Queue number: 0
[18:26:40] Fine-tune is in the queue. Queue number: 0
[18:29:14] Fine-tune started
[18:30:52] Completed epoch 1/4
[18:32:11] Completed epoch 2/4
[18:33:30] Completed epoch 3/4
[18:34:49] Completed epoch 4/4
[18:35:08] Uploaded model: **ada:ft-x-xxxx-xx-xx**
[18:35:09] Uploaded result file: file-aBcDeFgHiJ
[18:35:09] Fine-tune succeeded
```

我们现在已经有了可以使用的新模型。在下面的例子中，我使用了一个类似的句子，引擎会将文本分类为`spam`:

```
openai api -k sk-YOUR_KEY completions.create -m ada:ft-x-xxxx-xx-xx -M 4 -p "Hello, this is John from Finance Plus. I've called before,  We've helped other individuals like you improve their credit. Please give me a call later.###"
```

答案应该是这样的:

```
Hello, this is John from Finance Plus. I've called before,  We've helped other individuals like you improve their credit. Please give me a call later.###**spam**
```

如果你使用 Java，你可以尝试这样做

从性能角度来看，执行完成 API 似乎需要 500-900 毫秒，但是根据我的经验，你用得越多，它就变得越快。

将这种方法与人工智能和 GPT-3 结合使用，我们能够在筛选电话的同时扫描垃圾邮件，并实时通知我们的[呼叫助理](https://www.callassistant.ai)用户，他们存在垃圾电话。

感谢阅读。