# 如何将 Dialogflow 与 GPT-3 集成

> 原文：<https://betterprogramming.pub/how-to-integrate-dialogflow-with-gpt-3-creating-a-personal-virtual-mental-health-assistant-from-fee7d363993a>

## 从头开始创建虚拟精神健康助手

![](img/72100d0d3f92cebd4c3c6cd140dad2da.png)

dialog flow——Google 的自然语言理解平台是一个受欢迎的工具，它使设计对话式用户界面并将其集成到您的应用程序中变得容易。您可以使用 Dialogflow 来构建聊天机器人、语音助手和其他可以大规模模拟人类对话的代理。

另一方面，GPT-3 是 OpenAI 最大的语言模型，因其产生令人印象深刻的输出的能力而臭名昭著，经常欺骗人类认为它是由人类编写的。例如，该模型可以生成问题和答案对、描述，甚至博客帖子。该模型非常先进，甚至可以在一次竞赛中击败专业文案。

![](img/0f8a321e6dfcd0215116376504ade54f.png)

随着训练参数数量的增加，人类检测文本是否是人工智能生成的能力急剧下降。GPT-3 制造的假新闻已经很难与真新闻区分开来。在其中一个实验中，结果显示只有 50%的假新闻能够被检测出来！

前一段关于 GPT 3 号的内容是由 GPT 3 号通过完成我给定的提示生成的。

![](img/e340b16a76fc0f68281570a1dbec6768.png)

由于 GPT-3 可以生成高质量的输出，开发人员可以用它来创建聊天机器人、内容生成器和自然语言领域的许多其他应用程序。在本文中，我们将探索如何利用 GPT-3 的强大功能，并在我们的 Dialogflow 应用程序中利用它。对于本教程，我将创建一个虚拟的精神健康助手，它可以与其用户自由交谈，并提供一个安全的空间来表达自己的感受。让我们开始吧。

# 第一步:创建你的 OpenAI 账户

你必须注册并创建一个 [OpenAI 账户](https://beta.openai.com/signup)来接收你的 API 令牌。不用担心；它是免费的。你只需验证你的电话号码，你将获得价值 18 美元的免费积分，这给了我们足够多的请求来试验和玩这个模型。创建您的帐户后，您可以登录并从仪表板查看游乐场。

# 步骤 2:设置您的 Dialogflow 代理

创建一个新的 [Dialogflow](https://dialogflow.cloud.google.com/) 代理，它将与 GPT-3 集成。给你的经纪人一个名字，我们就搞定了。对于这个项目，我们将只使用提供的默认欢迎和回退意图。

![](img/ef058d4166a04a5c8c1951bd470017df.png)

# 步骤 3:配置欢迎和回退意图

每当用户用“嗨”问候机器人时，就会触发默认的欢迎意图每当机器人不理解用户输入时，就会触发回退意图。因为我们没有在我们的 bot 中添加任何新的意图，所以除了“Hi/hello”之外的所有用户话语都将触发默认的回退意图。我们可以通过在我们的 webhook 实现中进行 API 调用来使用这个后备意图与 GPT-3 进行通信。现在，删除欢迎意图中的默认响应，并启用 webhook 调用来实现它。

![](img/e0f97be53481237a0826d4173f9f8505.png)

对于回退意图，启用 webhook call for fulfillment 来调用 GPT-3 API，并根据用户的查询做出响应。如果 API 由于冷启动而失败或花费太长时间(超过五秒)，我们会添加一个默认响应“您能再说一遍吗”，这样我们就可以捕获用户话语，并在失败时再次调用 API。

![](img/f37468f9d0e1732423da623b940d2525.png)

# 步骤 4:编写 Webhook 实现

为了创建我们的虚拟精神健康机器人，我们需要使用 [Webhook](https://en.wikipedia.org/wiki/Webhook) 调用我们在 Dialogflow 中的实现来将我们的自定义逻辑添加到意图中。为此，我们必须创建自己的实现 API 来处理 Dialogflow 用户请求，并获取 GPT-3 生成的响应。我将使用 Node.js 和 Axios 作为我的服务器，但也可以随意使用任何其他框架。让我们开始编码吧。

我们为默认的 Welcome & Fallback 意图添加了处理函数，这些函数将在意图被触发时执行。对于回退意图处理程序来说，重要的是我们创建了一个函数 query GPT——它接收用户的话语并向 GPT-3 完成端点发出 POST 请求。该函数将 AI 响应发送回用户，并等待另一个查询。简单，嗯？

## 让我们来看看提示

GPT-3 只根据提供给模型的输入提示生成文本。微调这个输入提示对于用 GPT-3 获得好的结果是至关重要的。这催生了一个新的研究领域，叫做[快速工程](https://docs.cohere.ai/prompt-engineering-wiki/)，旨在产生最佳结果。为了创建我们的精神健康助手，下面是我选择的提示:

“以下是与一个可以与用户进行有意义对话的 AI 助手的对话。助手乐于助人、善解人意且友好。它的目标是让用户通过感觉被听到而感觉更好。随着每一次回应，人工智能助手都会提示用户自然地继续对话。”

# 步骤 5:托管 Webhook API

现在我们需要的东西都有了。是时候托管我们的 API 了，这样就可以通过 Dialogflow 在互联网上访问它。我们可以使用许多免费的云服务提供商，如 [Netlify](http://netlify.com) 或 [Vercel](http://vercel.com) 来托管我们的 API。但是由于这不是一个生产应用程序，只是为了演示，我使用了 [Repl.it](http://repl.it) 来托管我的节点服务器。要开始，只需注册到 [repl.it](http://repl.it) ，创建一个新的节点项目，并粘贴上面的 Webhook 实现代码。

![](img/a92ea0170dfa69cb153c2aa0c8287863.png)

要查询 GPT-3 API，您将需要它的访问令牌，您可以在 OpenAI 仪表板的“查看 API 密钥”中找到它。永远不要在代码中直接暴露你的密钥。总是使用环境变量来存储机密。复制您的 API 令牌，转到 Repl 中的 Secrets 选项卡，将其粘贴为新环境变量“`OPENAI_API_KEY`”的值，然后保存。

![](img/70a207eb9d019a13067628cc077ee866.png)

现在，您的代码可以安全地访问 API 令牌并请求 GPT-3！点击 Run，服务器就部署好了。然后，您将找到您的服务器的已发布端点 URL。

# 最后一步:在 Dialogflow 中更新 Webhook URL

最后一步是在 Dialogflow 中启用 Webhook Fulfillment，并将端点的 URL 粘贴到 Repl 服务器中，然后点击 save。请确保在您的 repl URL 末尾添加“/dialogflow ”,以访问我们的 POST 端点。

![](img/c76c4d2d1447e620011ccab72afeb01c.png)

就是这样！您的对话流代理现在应该通过回退意图与 GPT-3 集成。剩下要做的就是测试你的心理健康助理。

# 结果呢

你可以在这里自己测试这个机器人:[https://amagastya.com/gpt3](https://amagastya.com/gpt3)

![](img/07df3e1e2ef07b866b5b1457f4732af8.png)![](img/2252cc551908cf4cedd9b2b17ff4a9ea.png)![](img/6921939df32601c038b5e6a044cc9abe.png)

悲伤的确是生活的一部分，哈哈

![](img/444ff5066c0aba1658daf7878f817244.png)![](img/be9f4b812f4017d8e7d5e1c9e4bd3945.png)

# 结论

正如你所看到的，它并不完美，但机器人的大部分工作。机器人可以与用户进行有意义的对话，甚至表现出关心和同情的迹象。

请记住，我们没有添加任何意图，整个对话仅由 GPT-3 处理。用 Dialogflow 实现这个结果几乎是不可能的，因为我们永远无法准确预测用户可能会说什么，所以考虑所有可能的意图将是一个巨大的挑战。

然而，通过 GPT-3，我们可以处理这种复杂的开放式对话，使这些通用的开放领域情况成为大型语言模型的一个伟大用例，因为它们可以轻松地模拟和维护人类对话。

请注意，通过更改您的输入 GPT-3 提示，您可以用您的机器人创建酷和古怪的体验。我希望这能激发你创造自己的机器人体验，并从中获得乐趣。🙂

所以，我想用一首短诗来结束这篇文章:

![](img/a89bee013c8793eee1ea8a785d1f94de.png)

我希望你喜欢创建自己的 GPT-3 动力虚拟助理。敬请关注更多内容！

想要连接吗？请在 Linkedin 上关注我，网址是 https://www.linkedin.com/in/amoghagastya/[和 https://amagastya.com](https://www.linkedin.com/in/amoghagastya/)[的](https://amagastya.com)

也读—

[](/how-to-give-your-chatbot-the-power-of-neural-search-with-openai-ebcff5194170) [## 使用 GPT 3 优化你的聊天机器人的对话智能

### 用 OpenAI 赋予你的聊天机器人神经搜索的能力

better 编程. pub](/how-to-give-your-chatbot-the-power-of-neural-search-with-openai-ebcff5194170) [](/how-to-finetune-gpt-3-finetuning-our-virtual-mental-health-assistant-641c1f3b1ef3) [## 微调 GPT-3 使用 Python 创建一个虚拟精神健康助理机器人

### 为特定任务构建您自己的 ChatGPT

better 编程. pub](/how-to-finetune-gpt-3-finetuning-our-virtual-mental-health-assistant-641c1f3b1ef3)