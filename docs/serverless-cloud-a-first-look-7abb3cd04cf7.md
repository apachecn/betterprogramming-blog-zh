# 无服务器云:初步观察

> 原文：<https://betterprogramming.pub/serverless-cloud-a-first-look-7abb3cd04cf7>

## 知道使用无服务器云可以做什么吗

![](img/cc5ce1569c6c50c7e1ea29a3932e9048.png)

由[本杰明·戴维斯](https://unsplash.com/@bendavisual?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

无服务器云第一次引起我的注意是在 2021 年无服务器峰会期间，当时 AWS 无服务器英雄[杰里米·戴利](https://www.jeremydaly.com/)展示了他一直在帮助自己构建的产品。

对于任何对无服务器感兴趣的人来说，如果他之前没有遇到过杰里米，我肯定他不会介意我链接到他的内容，我强烈建议订阅他的时事通讯“Off-by-none ”,过去几年我一直用它来了解无服务器世界。

首先，让我们回答房间里的大象:什么是无服务器云？

![](img/4b31a7c5c1b2e5df4b6f01a363fa7cef.png)

无服务器云允许您构建您的应用程序，而不必担心云服务的创建和管理，让开发人员有更多时间专注于他们最擅长的事情；写代码。它通过一个接口抽象云服务来做到这一点，您可以将该接口作为依赖项添加到您的应用程序中，让无服务器云来完成所有繁重的工作。

无服务器云还提供了一个仪表板，允许您使用现成的指标和日志来监控您的应用程序。它还允许您直接从仪表板调用您的端点，从而不再需要像 Postman 这样的工具来执行相同的功能。

现在到了有趣的部分:我可以用无服务器云做什么？

# 储存；储备

无服务器存储使您能够存储文件，而不必将基础架构编写为代码代码来自己创建和管理 S3 存储桶，您甚至不需要将存储桶的链接传递到您的应用程序中。

存储是现代软件应用程序中非常常见的用例。在下面的例子中，你可以看到我调用存储 API 来存储一个雇员的简历，并获取我在响应中返回的文件的下载 URL。

```
import { api, data, storage, schedule, events } from "@serverless/cloud";api.post("/employees/:id/cv", async (req, res) => {let key = "employee:" + req.params.id;
let employee = await data.get(key);
let filepath: string = '/cv/' + req.params.id + '.txt';await storage.write(filepath, req.body);let downloadUrl = await storage.getDownloadUrl(filepath);   employee.cvUrl = downloadUrl;await data.set(key, employee, true);res.send({ downloadUrl });
});
```

storage service 的另一个很酷的特性是能够为 bucket 中的任何变化添加监听器。这非常酷，你通常必须在你的配置中处理这些事件，例如当你的桶中有任何变化时调用一个 lambda。

下面的代码会在我的 bucket 中存储带有前缀`cv`的文件时写一条日志消息。您可以使用此功能来构建异步任务的管道，例如，在从应用程序中检索文件之前，扫描上传的文件中的病毒或编辑文件中的敏感数据。

```
import { api, data, storage, schedule, events } from "@serverless/cloud";storage.on("write:cv/*", async (event) => { console.log('File has been uploaded, scanning for virus: ' + event.path);});
```

你可以在这里看到存储服务[的其他功能。](https://www.serverless.com/cloud/docs/apps/blob-storage)

# 数据

无服务器数据使您能够将应用程序数据存储在键/值数据存储中，而不必管理数据库访问、维护、调整，甚至不必将连接字符串传递给应用程序。

数据是软件的基石，因此通过调用方法来存储和检索应用程序数据有可能减少团队的大量开发时间。在下面的例子中，你可以看到我是如何检索所有雇员的列表、获取单个雇员并存储一个雇员的。

```
import { api, data, storage, schedule, events } from "@serverless/cloud";
import { Employee } from "./models/Employee";
import { v4 as uuidv4 } from 'uuid';api.get("/employees", async (req, res) => {let result = await data.get("employee:*", true);res.send({ employees: result.items });
});api.get("/employees/:id", async (req, res) => {
let key = "employee:" + req.params.id;let result = await data.get(key);res.send({ employee: result });
});api.post("/employees", async (req, res) => {
let employee: Employee = req.body;
let id: number = uuidv4();await data.set('employee:' + id, employee);events.publish("employee.created", {    name: employee.name  });res.send({ employeeId: id });
});
```

你可以在这里看到数据服务[的其他特性。](https://www.serverless.com/cloud/docs/apps/data)

# 事件

无服务器事件允许您发布事件，然后异步处理它们，可以立即处理，也可以在一段时间后处理。同样，这允许您构建任务的异步管道，而不必创建队列和配置 lambda 调用。

在下面的例子中，您可以看到我是如何在创建一个员工时发布一个事件，然后异步获取该事件的。您可以使用它来发送欢迎电子邮件，或者让应用程序的其他集成(如工资系统)知道这个新员工已经创建。

```
import { api, data, storage, schedule, events } from "@serverless/cloud";
import { Employee } from "./models/Employee";
import { v4 as uuidv4 } from 'uuid';api.post("/employees", async (req, res) => {
let employee: Employee = req.body;
let id: number = uuidv4();await data.set('employee:' + id, employee);events.publish("employee.created", {    name: employee.name  });res.send({ employeeId: id });
});events.on("employee.created", async ({ body }) => { console.log('Send welcome email to ' + body.name);});
```

你可以在这里看到事件服务[的其他特性。](https://www.serverless.com/cloud/docs/apps/events)

# 计划任务

我们都参与过需要运行 cron 作业来运行清理脚本或触发数据更新的项目。有了无服务器云，您只需定义一个函数，就可以随时运行，无需配置 cron 作业在您的环境中运行。

```
import { api, data, storage, schedule, events } from "@serverless/cloud";schedule.every("1 day", () => {console.log("Doing some sort of cleanup task every night!");});
```

您可以在这里看到调度任务服务[的其他特性。](https://www.serverless.com/cloud/docs/apps/schedule)

# 应用和实例

无服务器云遵循隔离模型。你有一个组织，你可以在其中创建许多应用程序，这些应用程序可以是多栈或独立的服务。每个应用程序可以有许多实例，一个实例可以是一个环境，如生产或测试环境，每个实例都有自己的一组资源，如数据和存储。

这种方法让你在几秒钟内就可以在云中运行你的应用程序，几乎快得令人难以置信。

想要在云中运行您的应用程序吗？只需键入`cloud`

想要与团队成员分享您的当前状态以进行测试吗？只需输入`cloud share`

想要部署到测试环境吗？只需键入`cloud deploy test`

想要将测试环境中的应用程序版本升级到生产环境中吗？只需键入`cloud promote test prod`

这几乎太容易了。

# 当前的限制

无服务器云仍在开发中(撰写本文时版本为 2.8.2 ),因此仍有许多功能尚未推出，所以我希望在未来的版本中看到以下内容:

*   能够选择部署云服务的区域。目前，所有数据都存储在`us-east-1`地区，但是许多公司可能会受到法律约束，不得将数据传输到其所在国家/地区之外，因此他们无法使用无服务器云。
*   组织内部的细粒度访问控制。目前，您存储应用程序和数据的组织内没有基于角色的访问控制，这意味着如果您想要锁定对生产数据的访问，您必须创建一个单独的组织来托管它，这将允许您使用 cool promote 功能。
*   对底层云资源的访问。我认为无服务器云面临的最大挑战之一是建立一种声誉，让顾客、客户和企业在移交数据控制权时感到安全。我认为需要有一个中间地带，客户可以在需要时访问底层云服务。

# 最后的想法

我非常喜欢使用无服务器云的体验，我将继续使用它来部署个人和概念验证应用程序。在未来，我希望上述几点能够得到解决，这将使我能够在组织内推动无服务器云的使用。

如果你想看看上面链接的项目代码，你可以在这里找到。

无服务器云有一个很棒的 Slack 社区，它会很乐意回答你的任何问题，解决你可能遇到的任何问题，你可以在这里加入那个[。](https://serverless-contrib.slack.com/join/shared_invite/zt-d5qzowja-pnOerTzAIZUrN18hWYUIHA)

```
**Want to Connect?**If you have any comments about this blog or want to have a chat you can find me on LinkedIn [here](https://www.linkedin.com/in/shaun-ganley-8068aa82/).
```