# Firebase 云函数:Git 和 GitHub GraphQL API

> 原文：<https://betterprogramming.pub/firebase-cloud-functions-git-github-graphql-api-5c8577591cb1>

## 使用 Git 命令和 GitHub GraphQL API 在 Firebase Cloud 函数中创建一个 pull 请求

![](img/01e18ac86454729719daa2ac1a6f21f2.png)

背景照片由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我们最近在 [DeckDeckGo](https://deckdeckgo.com) 发布了一个令人兴奋的新特性。

除了能够将您的演示作为渐进式网络应用部署到网上，我们的网络开源编辑器现在还可以将您的源代码推送到 [GitHub](https://github.com) 。

这个新函数运行在 Firebase 的[云函数上。因为我们喜欢分享我们的发现，这里是我们在开发这种集成时学到的关键东西。](https://firebase.google.com/docs/functions/)

# 访问令牌

要与 GitHub 交互，我们需要一个令牌。

## 个人代币

如果你想用你的账户与 GitHub 互动，你可以使用一个[个人访问令牌](https://github.com/settings/tokens)。创建后，您可以在我们的 Firebase 函数配置中设置它。这样做，它将从你的代码混淆。

```
#!/bin/shfirebase functions:config:set github.token="4a686......."
```

## Firebase 身份验证和 GitHub 令牌

如果你对与 GitHub 交互感兴趣，看看你的用户的行为，你可以使用 [Firebase UI](https://github.com/firebase/FirebaseUI-Web) 和 [Firebase 认证](https://firebase.google.com/docs/auth/)。

据我所知，采用这样的组合，不幸的是无法在 Firebase 云函数中获得用户的 GitHub 令牌。我试图钩住认证[事件](https://firebase.google.com/docs/functions/auth-events)，但是在被触发的对象中没有找到任何相关信息。

我可能遗漏了一些东西——在这种情况下，请尽快让我知道——但如果没有，要获得这样的信息，您必须通过 Firebase UI 配置的`signInSuccessWithAuthResult`回调来找到它。

```
callbacks: {
  signInSuccessWithAuthResult: 
    (authResult: firebase.auth.UserCredential, _redirectUrl) => { const token: string =
      (userCred.credential as 
               firebase.auth.OAuthCredential).accessToken;

    return true;
  },
},
```

**注意:**我提出了一个问题，询问如何使用 TypeScript 访问令牌，而到`OAuthCredential`的转换是作为[答案](https://github.com/firebase/firebaseui-web/issues/743)提供的。

# 文件系统

在继续之前，你可能会问自己，我们将如何能够在云中执行 Git 命令？我实际上也在问自己同样的问题，结果是云函数可以访问他们的[文件系统](https://cloud.google.com/functions/docs/concepts/exec#file_system)的一个临时文件夹。

> “文件系统唯一可写的部分是`/tmp`目录，您可以用它来存储函数实例中的临时文件。这是一个称为“tmpfs”卷的本地磁盘装载点，写入卷的数据存储在内存中。请注意，它将消耗为该函数提供的内存资源。— [德斯蒙德·卢阿](https://code.luasoftware.com/tutorials/google-cloud-functions/cloud-functions-read-write-temp-files/)

此外，临时目录不能跨函数共享。例如，这意味着您不能使用这样的文件夹来共享数据。

`tmp`订单还没有硬编码。相反，可以使用 [Node.js OS 模块](https://nodejs.org/api/os.html)来检索临时文件夹。不管怎么说，万一将来发生变化，使用它可能会更方便——你永远不知道。

```
import * as os from 'os';console.log(os.tmpdir()); // -> /tmp
```

通过与`[path](https://nodejs.org/api/path.html)` [模块](https://nodejs.org/api/path.html)一起使用，我们甚至可以创建一个简短的实用函数来本地解析文件的路径。

```
import * as path from 'path';
import * as os from 'os';export function getFilePath(...files: string[]): string {
  return path.join(os.tmpdir(), ...files);
}console.log(getFilePath('yo', 'david.txt'); // -> /tmp/yo/david.txt
```

# Git 命令

为了克隆一个 repo 或者一般来说执行任何 Git 命令(比如`commit`、`pull`或者`push`)，我建议使用[史蒂夫·金](https://github.com/steveukx)开发的 Node.js 的[简单 Git](https://www.npmjs.com/package/simple-git) 接口(在 [npm](https://www.npmjs.com/package/simple-git) 上每周 150 万次下载)。它确实减轻了所有的工作。

```
npm i simple-git --save
```

## 克隆

具体地，克隆功能可以如下实现:

```
import * as path from 'path';
import * as os from 'os';import simpleGit, {SimpleGit} from 'simple-git';export async function clone(repoUrl: string, repoName: string) {
  const localPath: string = path.join(os.tmpdir(), repoName); await deleteDir(localPath);

  const git: SimpleGit = simpleGit();

  await git.clone(repoUrl, localPath);
}// Demo:(async () => {
 await clone('[https://github.com/deckgo/deckdeckgo/](https://github.com/deckgo/deckdeckgo/)', 'deckdeckgo'); 
})();
```

即使临时文件夹很可能是空的，也可以先删除工作子目录。这就是为什么我在上面的函数中调用`deleteDir`。

```
import * as rimraf from 'rimraf';export function deleteDir(localPath: string): Promise<void> {
  return new Promise<void>((resolve) => {
    rimraf(localPath, () => {
      resolve();
    });
  });
}
```

如你所见，我使用的是来自 [Isaac Z. Schlueter](https://twitter.com/izs) 的`[rimraf](https://github.com/isaacs/rimraf)`(在 [npm](https://www.npmjs.com/package/rimraf) 上的 3700 万周下载量)。

```
npm i rimraf --save && npm i @types/rimraf --save-dev
```

## 推

Git 命令的另一个有趣的例子是`push`请求，因为我们必须使用令牌来认证请求。

在搜索了使用令牌的解决方案之后，我特别花了一些时间阅读这个[堆栈溢出](https://stackoverflow.com/questions/18935539/authenticate-with-github-using-a-token)问答。我得出的结论是，即使我们在函数中执行交互，避免暴露令牌的最佳解决方案是在 Git URI 中使用它。

**注意:**令牌暴露在潜在的错误消息中——这就是为什么我认为适当地捕捉这些消息也是好的。

除了令牌，我们可能需要提供 GitHub 账户的`username`(例如 [peterpeterparker](https://github.com/peterpeterparker) )和`email`。这些信息也可以通过我们的功能配置来管理。

```
import * as functions from 'firebase-functions';import * as path from 'path';
import * as os from 'os';import simpleGit, {SimpleGit} from 'simple-git';export async function push(project: string,
                           branch: string) {
  try {
    const localPath: string = path.join(os.tmpdir(), repoName); // Git needs to know where is has to run, that's why we pass
    // the pass to the constructor of simple-git const git: SimpleGit = getSimpleGit(localPath); // Configure Git with the username and email const username: string = functions.config().github.username;
    const email: string = functions.config().github.email; await git.addConfig('user.name', username);
    await git.addConfig('user.email', email); // Finally Git push const token: string = functions.config().github.token; await git.push(`https://${username}:${token}@github.com/${username}/${project}.git`, branch);
  } catch (err) {
    throw new Error(`Error pushing.`);
  }
}// Demo:(async () => {
 await push('deckdeckgo', 'my-branch'); 
})();
```

# GitHub GraphQL API

GitHub API 的最新版本(v4)可以用于 GraphQL 查询，这取决于您的观点。它的[文档](https://docs.github.com/en/graphql)使得搜索信息变得相对容易，但是[浏览器](https://developer.github.com/v4/explorer/)和它的自动完成功能可能更便于快速灵活地编写查询。

## 询问

我没有使用任何 GraphQL 客户端(例如， [Apollo](https://github.com/apollographql/apollo-client) )来执行查询。相反，我开发了一个实用程序来执行 HTTPS 请求。

```
import fetch, {Response} from 'node-fetch';async function queryGitHub(githubToken: string, 
                           query: string): Promise<Response> {
  const githubApiV4: string = 'https://api.github.com/graphql';

  const rawResponse: Response = await fetch(`${githubApiV4}`, {
    method: 'POST',
    headers: {
      Accept: 'application/json',
      'Content-Type': 'application/json',
      Authorization: `token ${githubToken}`,
    },
    body: JSON.stringify({query}),
  });

  if (!rawResponse || !rawResponse.ok) {
    throw new Error('Cannot perform GitHub query.');
  }

  return rawResponse;
}
```

由于`fetch`在 Node.js 中不是本地可用的，所以我使用了[节点获取](https://github.com/node-fetch/node-fetch)(在 [npm](https://www.npmjs.com/package/node-fetch) 上每周 1600 万次下载)。

```
npm i node-fetch --save && npm i @types/node-fetch --save-dev
```

## 查询:用户信息

下面是一个相对基本的查询示例。在这个函数中，我们尝试检索与我们用来认证请求的令牌相对应的 GitHub `login`(用户名)和`id`，分别是当前认证用户的信息。

```
export interface GitHubUser {
  id: string;
  login: string;
}export function getUser(githubToken: string): Promise<GitHubUser> {
  return new Promise<GitHubUser>(async (resolve, reject) => {
    try {
      const query = `
        query {
          viewer {
            id,
            login
          }
        }
      `;

      const response: Response = 
            await queryGitHub(githubToken, query);

      const result = await response.json();

      resolve(result.data.viewer);
    } catch (err) {
      reject(err);
    }
  });
}// Demo:(async () => {
 const token: string = functions.config().github.token;

 const user = await getUser(token);  console.log(user); // -> {login: 'peterpeterparker', id: '123'}
})();
```

## 变异:拉请求

创建一个拉请求不是 GraphQL 查询，而是一个[变异](https://docs.github.com/en/graphql/reference/mutations#createpullrequest)。与前面的查询相比，它需要更多的信息，但是它背后的逻辑是相同的:编写一个 GraphQL 查询/变异，通过 HTTPS 请求发送它，然后获得结果。

值得注意的是，为了创建一个 PR，突变将需要一个`repositoryId`。这个信息可以在另一个 GraphQL 查询的帮助下找到——例如，在请求[存储库](https://docs.github.com/en/graphql/reference/queries#repository)信息时提供。

```
export function createPR(githubToken: string,
                         repositoryId: string,
                         branch: string): Promise<void> {
  return new Promise<void>(async (resolve, reject) => {
    try {
      const title: string = 'feat: my title';
      const body: string = `# The Pull Request body.

It supports *Markdown*.`; // We want to provide a PR from a branch to master const query = `
        mutation CreatePullRequest {
          createPullRequest(input:{baseRefName:"master",body:"${body}",headRefName:"${branch}",repositoryId:"${repositoryId}",title:"${title}"}) {
            pullRequest {
              id
            }
          }
        }
      `;

      const response: Response = 
            await queryGitHub(githubToken, query);

      const result = await response.json();

      if (!result || !result.data || 
          !result.data.createPullRequest || result.errors) {
        resolve(undefined);
        return;
      }

      resolve();
    } catch (err) {
      reject(err);
    }
  });
}// Demo:(async () => {
 const token: string = functions.config().github.token;

 await createPR(token, '6789', 'my-branch');
})();
```

# 摘要

在开发这个特性的过程中，我学到了很多新东西，我希望在这篇文章的帮助下，我能够分享我的主要发现。

此外，我们是开源的——你可以随时查看我们的 [repo](https://github.com/deckgo/deckdeckgo/) 的源代码，或者为我们的项目做出贡献。

也非常欢迎你在下一次演讲中尝试一下。

我也期待着检查和尝试包含您的幻灯片源代码的 GitHub repos。

到无限和更远的地方！