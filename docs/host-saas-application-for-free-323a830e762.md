# 如何创建一个 SaaS 应用程序并在 Vercel 上托管它

> 原文：<https://betterprogramming.pub/host-saas-application-for-free-323a830e762>

## 快速托管、预览和交付多租户 SaaS 应用

![](img/5d0b4ae907638feaa9efd0bf42515013.png)

照片由[你好我是尼克](https://unsplash.com/@helloimnik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

现代技术增强了我们的日常编码体验。几年前，我们需要大量的能力来实现一个简单的网站和托管它。时至今日，许多人仍然相信，为了构建和交付一个应用程序，我们需要一个多学科的团队。

在许多复杂的场景中确实如此，但是对于许多初创公司和简单的项目，我们使用的方法已经彻底改变了。得益于新技术，拥有基本开发能力的前沿开发人员或专业人员可以创建自己的应用程序。

在本文中，我们将看到如何利用云技术，通过使用 Vue.js 和几行配置来实现多租户应用程序。换句话说，我们将学习如何创建一个可供多个用户使用的基本 SaaS 应用程序。此外，该解决方案可以托管在一个 Vercel fee 层中，启动时无需花费一分钱。

让我们看看这是怎么可能的。

# 路线图

在本文中，我们将讨论许多对理解该解决方案很重要的问题。你可以在 GitHub 上找到与这个例子相关的所有源代码(文末链接)。演示应用程序是一个笔记本，允许用户在云端写笔记，就像 Evernote 或 One Note 等生产力工具一样。

因为一行一行地解释所有的源代码是不可能的，所以我将把重点放在最重要的步骤上:

1.  如何在不是后端开发者的情况下实现后端
2.  实现前端
3.  如何免费托管应用程序

让我们一步一步开始吧！

# 如何免费托管应用程序

这个应用程序使用 Vercel 来托管前端和后端。在我写这篇文章的时候，Vercel 有一个免费层来托管任何前端应用程序(参见[价格页面](https://vercel.com/pricing#plan-compare)了解限制)。

非常有趣的部分是对无服务器功能的支持，事实上，这使得应用程序功能齐全。在免费层，我们有 100 GB 小时的限制(每个请求使用的 RAM 乘以时间的乘积)，但可以通过购买付费计划来克服。

Vercel 直接连接到您的 git 存储库，构建源代码并发布它。如果您想了解更多信息，可以阅读文章“ [*使用 Vercel*](https://medium.com/better-programming/how-to-deploy-an-existing-application-with-vercel-cfcd314e5396) 部署应用程序”。

因为所有我们需要知道的已经在上面提到的文章中被告知，我将只解释最重要的步骤。

1.  首先，你要在 Vercel 上创建一个项目。完成后，您将看到下一个屏幕:

![](img/8f82866650f13da0fef36855d2b7bdcd.png)

创建项目后。

2.你必须点击“连接”并选择你的 git 库。请参见下图

![](img/38779e742f66b6afd00416e9c568e664.png)

选择正确的 git 存储库

3.在下一次推送到主分支时，应用程序将被发布。之后，您将在项目面板中看到项目状态、链接和详细信息，如下图所示。

![](img/3d41003f44f613e139c9be268de34b2e.png)

Vercel 中的项目面板

既然我们找到了托管的解决方案，让我们看看如何实现后端。

# 如何在不是后端开发人员的情况下实现后端

其中最关键的部分就是开发者的专业化。许多前端开发人员有很好的想法，但当开始实施时，他们发现没有强大的后端体验很难做一些事情。

大多数时候，这种差距可以通过采用无头的低代码平台来填补，如 } else {
return Promise.reject(error);
}});

现在我们可以实现一个登录表单和注册表单。它们都是简单的 Vue 组件，所以我在这里只报告登录组件(你可以在 GitHub 上找到所有的源代码)。我使用的模板如下

```
<v-container>
    <v-row class="text-center">
      <v-col>
       <h1>login</h1>
       <v-form>
        <v-text-field v-model="username" type="text" label="login" />
        <v-text-field v-model="password" type="password" label="Password"/>
        <v-btn @click="save" block >Save</v-btn>
       </v-form>
      </v-col>
    </v-row>

    <v-row class="text-center">
      <v-col>
       <v-subheader> Not an user? <a href="#" @click="$router.push({name:'register'})">Register</a></v-subheader>
      </v-col>
    </v-row>
  </v-container>
```

javascript 部分由下一段 javascript 描述:

```
export default {
    name: 'Login',
    beforeMount:async function(){

    },
    methods:{
        save: async function(data){
            let response=await this.apiCall('/api/handler','POST',{username:this.username, password:this.password},{ action:"login"});

            if(response.status !=200 || response.data.hasError)
            {
              this.showError("login failed");
            }
            else
            { 
              let user=response.data.data;
              this.showSuccess("Hello "+user.name);
              localStorage.setItem('userToken',user.token);
              this.$router.push({name:'notes'});
            }
        }
    },
     data: () => ({
      token:"",
      username:"",
      password:"",
      })
  }
```

最后，我们可以使用已经存在的后端来实现我们的应用程序！

# 结论

由于现代技术，创建多租户应用程序不再需要成为专家，由于云服务，托管不再痛苦。

当然，当应用程序的需求变得具有挑战性时，我们将需要面对复杂性(云的成本将变得相关，并且您仍然需要团队中不止一个成员)。

然而，对于一个简单的项目，MVP 或初创公司来说，有一个简单的启动方式是非常重要的。随着项目变得越来越苛刻，我们将不得不在可持续发展中扩展应用程序。

参考资料:

*   CrudIt 框架可以在 GitHub 上获得，由 NPM 交付。更多信息见项目页面[https://github.com/zeppaman/crudit](https://github.com/zeppaman/crudit)
*   你可以在官网上了解更多关于 [Vercel](https://vercel.com/) 的信息
*   你可以在这里下载这个例子[的源代码。](https://github.com/zeppaman/crudit-demo)