# GitHub 操作中的谷歌云无钥匙认证

> 原文：<https://betterprogramming.pub/google-cloud-keyless-authentication-in-github-actions-9dccac4601c>

## 什么是无钥匙认证，为什么你会关心？

![](img/09d4c3de01dc0c71a348b32336f49dfa.png)

照片由 [Towfiqu barbhuiya](https://unsplash.com/@towfiqu999999?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

假设您想要直接从 GitHub Actions 运行完整的 CI/CD 流。作为部署的最后一步，您可能需要向 Google Cloud 进行身份验证，以部署对基础架构和服务的任何更改。因为您没有 UI，所以通常通过服务帐户执行身份验证。

例如，在 GitHub Actions 中运行以下两种场景时，您需要提供 Google Cloud Platform (GCP)的凭证:

*   使用 Terraform 应用基础架构更改
*   使用 [gcloud CLI](https://cloud.google.com/sdk/gcloud) 对您的云服务进行更改

GCP 提供了一种简单的机制:下载 JSON 格式的服务帐户密钥，并通过`[GOOGLE_APPLICATION_CREDENTIALS](https://cloud.google.com/docs/authentication/getting-started)`环境变量将其提供给任何正在运行的客户端。

这种机制是可行的，但是它依赖于长期有效的凭证。它可以工作，但也是不安全的，因为如果以某种方式暴露，第三方将可以访问服务帐户可以访问的所有内容，这为攻击者提供了充足的时间来窃取数据和/或进行一些破坏。

> 如果你认为你会发现任何这样的攻击，请三思！一个有动机和熟练的攻击者可以在触发任何警报之前走得很远，从而促使您进行调查。

进入[工作负载身份联盟](https://cloud.google.com/iam/docs/workload-identity-federation)。它允许授予外部身份(也称为 GitHub 主体)访问 Google 云资源的权限。它不仅神奇地做到了[而不需要任何服务密钥秘密，而且还设置了一个一小时的令牌到期时间——大大减少了攻击窗口。](https://github.com/google-github-actions/auth)

好了，现在我们知道为什么了让我们继续讨论“如何做”。

# 如何在 GitHub 和 GCP 之间配置无钥匙认证？

开始之前，您需要确定一些名称和变量:

*   `$PROJECT`:您的谷歌云项目的标识(即`some-project-123`)
*   `$NAME`:给身份池命名(即`my-identity-pool`)
*   `$PROVIDER_NAME`:给身份提供者起的名字(即`my-provider`)
*   `$SERVICE_ACCOUNT`:有权访问您的项目/资源的服务帐户用户的名称
*   `$GITHUB_ORG`:托管你的代码的 GitHub 组织
*   `$GITHUB_REPO`:存放你代码的 GitHub 库
*   `$PROJECT_NUMBER`:从 Google Cloud 检索，见下图:

```
gcloud projects list \
--filter="project_id:$PROJECT" \
--format="get(project_number)"
```

现在，让我们通过五个简单的步骤来配置 GCP 无钥匙认证:

1.  创建身份池

```
gcloud iam workload-identity-pools create "$NAME" \
--project="$PROJECT" \
--location="global" \
--display-name="$NAME (or a description of your choosing)"
```

2.创建身份提供者

```
gcloud iam workload-identity-pools providers create-oidc "$PROVIDER_NAME" \
--project="$PROJECT" \
--location="global" \
--workload-identity-pool="$NAME" \
--display-name="$NAME (or a description of your choosing)" \
--attribute-mapping="google.subject=assertion.sub,attribute.actor=assertion.actor,attribute.aud=assertion.aud,attribute.repository=assertion.repository" \
--issuer-uri="https://token.actions.githubusercontent.com"
```

根据配置，此身份提供者将允许您的工作流获取仅限于当前(调用)存储库的 OIDC 令牌—在安全方面，请始终应用[原则 _of_least_privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) ！

3.将策略绑定到您的服务帐户:

```
gcloud iam service-accounts add-iam-policy-binding "$SERVICE_ACCOUNT@$PROJECT.iam.gserviceaccount.com" \
--project="$PROJECT" \
--role="roles/iam.workloadIdentityUser" \
--member="principalSet://iam.googleapis.com/projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/$NAME/attribute.repository/$GITHUB_ORG/$GITHUB_REPO"
```

在 [GitHub 工作流程](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)端:

4.您必须将`[id-token permission](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#permissions-for-the-github_token)`赋予作业的`GITHUB_TOKEN`，以便运行的工作流可以读/写 OIDC 令牌并与 Google 身份提供者交互。

这很容易通过向您的工作(或整个工作流程)添加以下[权限块](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#permissions)来完成。

```
permissions:
  contents: read
  id-token: write
```

> 在 GH 工作流中指定显式权限时，您将依次删除所有默认权限。这就是为什么您需要“*内容:读取”行。*

5.最后，使用 [Google Auth GitHub 动作](https://github.com/google-github-actions/auth)来检索您的工作所需的凭证——您工作中的所有后续步骤都将能够使用这些凭证。

```
- name: 'Authenticate to Google Cloud'
  uses: 'google-github-actions/auth@v0.4.0'
  with:
    workload_identity_provider: 'projects/$PROJECT_NUMBER/locations/global/workloadIdentityPools/$NAME/providers/$PROVIDER_NAME'
    service_account: '$SERVICE_USER@$PROJECT.iam.gserviceaccount.com'
```

就是这样！

祝你自动化一切顺利！