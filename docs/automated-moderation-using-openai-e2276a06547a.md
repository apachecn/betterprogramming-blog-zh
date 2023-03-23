# 使用 OpenAI 进行自动审核

> 原文：<https://betterprogramming.pub/automated-moderation-using-openai-e2276a06547a>

## 利用人工智能工具最大化用户体验

![](img/95e8348f73e4861c6ee896cc0ead9565.png)

[乞力马扎罗山工作室](https://unsplash.com/@kilimanjarostudioz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

要开始，你需要在 [OpenAI](https://beta.openai.com/overview) 注册，[生成一个密钥](https://beta.openai.com/account/api-keys)。

接下来，我们将建立一个简单的 Nuxt 项目。只需使用跟随命令的[来搭建一个启动项目，并添加`openai`包(nuxt 文档更喜欢使用`yarn`，但也可以随意跟随`npm`或`pnpm`步骤):](https://nuxt.com/docs/getting-started/installation#new-project)

```
npx nuxi init openai.moderation
cd openai.moderation
yarn install
yarn add openai
yarn dev -o
```

这将导致 starter 项目运行在(通常是) [http://localhost:3000](http://localhost:3000/) 上。

现在，在您最喜欢的 IDE 中打开项目，让我们开始吧！

# 配置密钥

在包含这一行的项目根目录下创建一个`.env`文件(替换为您的个人密钥):

```
OPENAI_API_KEY=ALWAYSKEEPSECRETSTOYOURSELF
```

接下来，打开`nuxt.config.ts`，确保它看起来像这样:

```
export default defineNuxtConfig({
    runtimeConfig: {
        OPENAI_API_KEY: process.env.OPENAI_API_KEY,
    },
})
```

# 设置 API

为了与 OpenAI 端点通信，我们需要一台自己的服务器。

在 Nuxt 中，添加 API 端点就像在`server/api`文件夹中添加文件一样简单。

因此，首先创建文件夹结构，并将以下内容放在名为`moderate.post.ts`的文件中:

```
export default defineEventHandler(event => {
    const body = await readBody(event)
    return body?.message
})
```

这将返回我们发送到`/api/moderate`端点的任何内容(Nuxt 将为我们设置路由)。

# 输入组件

我们将创建一个小组件，它只接受文本输入，并在提交时到达我们设置的端点，以便我们可以验证响应。

在项目根目录的`components`文件夹中创建一个`Moderate.vue`组件。

让我们从使用[脚本设置符号](https://vuejs.org/api/sfc-script-setup.html#script-setup)定义脚本开始:

```
<script setup lang="ts">
const input = ref("");
const result = ref([]);

const onSubmit = async () => {
  const response = await $fetch("/api/moderate", {
    method: "post",
    body: { message: input.value },
  });
  result.value.unshift(response);
  input.value = "";
};
</script>
```

首先，我们设置一个句柄来处理输入和结果，我们定义一个句柄来调用我们已经设置好的端点，将输入作为消息属性附加到主体上。( [The。值是指可变和无功参考值](https://vuejs.org/api/reactivity-core.html#ref)

现在我们将添加一个包含以下内容的模板:

*   包含输入的小表单；
*   提交按钮将调用`onSubmit`处理程序；
*   显示端点输出的位置

你可以随心所欲的设计它，但是这并不是本教程的目的。直接把它粘贴到脚本标签下面:

```
<template>
  <div>
    <div class="input">
      <input type="text" v-model="input" />
      <button type="submit" @click="onSubmit">Validate moderation</button>
    </div>
    <div class="output">
      <ul>
        <li :key="i.id" v-for="i in result">
          {{ i.results }}
        </li>
      </ul>
    </div>
  </div>
</template>
```

现在保存这个文件，让我们将组件加载到`app.vue`上，将它的内容替换为:

```
<template>
  <div>
    <Moderate />
  </div>
</template>
```

现在，您应该看到组件正在本地主机上运行。一旦插入一些文本并点击提交，它应该由我们自己的端点返回，并作为列表项的一部分显示在组件中。

# 添加智能

最后，我们将更新`moderate.post.ts`文件以利用 OpenAI 的功能。 [moderation API](https://beta.openai.com/docs/guides/moderation) 是更简单的 API 之一，所以它是一个很好的入门工具。

我们不是立即返回`body.message`，而是首先通过用键实例化 OpenAI 客户机来配置它。然后，我们将使用消息的内容查询端点。这意味着我们还需要将处理程序改为一个`async`函数！

该文件应该如下所示:

```
import { Configuration, OpenAIApi } from 'openai';

// it's an async function now!
export default defineEventHandler(async (event) => {
    const body = await readBody(event)
// setup the configuration
    const configuration = new Configuration({
        apiKey: process.env.OPENAI_API_KEY,
    });
// instantiate the openaiClient
    const openaiClient = new OpenAIApi(configuration);

// Make the call to the moderation endpoint
    const res = await openaiClient.createModeration({
        input: body?.message,
      });

// return the result
    return res.data
})
```

就是这样。因此，您现在有机会通过非常积极地对待输入字段来测试这一点。您应该会看到按不同类别和等级对您的输入进行的评估，如下例所示:

```
{
  "id": "modr-XXXXX",
  "model": "text-moderation-001",
  "results": [
    {
      "categories": {
        "hate": false,
        "hate/threatening": false,
        "self-harm": false,
        "sexual": false,
        "sexual/minors": false,
        "violence": false,
        "violence/graphic": false
      },
      "category_scores": {
        "hate": 0.18805529177188873,
        "hate/threatening": 0.0001250059431185946,
        "self-harm": 0.0003706029092427343,
        "sexual": 0.0008735615410842001,
        "sexual/minors": 0.0007470346172340214,
        "violence": 0.0041268812492489815,
        "violence/graphic": 0.00023186142789199948
      },
      "flagged": false
    }
  ]
}
```

如果您已经完成了这个示例，那么使用 OpenAI 的一个有趣方式就是使用[图像生成 API](https://beta.openai.com/docs/guides/images) 。

有了这个基础，你应该能够修改现有的代码或者在你喜欢的框架中进行你自己的集成。

在处理发布用户生成的内容时，使用这些工具会给你很大帮助。请记住，这只是一个示例，而不是真实的实现。此外，正如 OpenAI 建议的那样，在处理这类事情时，总是保持一些人的眼睛在手边。这个例子的一个有效用例是在发布之前先发制人地标记提交。

使用人工智能来减轻人类的负担而不完全消除他们，将是对当前能力的明智和良好的利用。人工智能，就像人类一样，仍然有缺陷，但我们可以利用它来帮助我们完成简单的任务。