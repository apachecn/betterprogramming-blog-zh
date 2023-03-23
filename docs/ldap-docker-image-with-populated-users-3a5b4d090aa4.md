# 构建一个由用户填充的 OpenLDAP Docker 映像

> 原文：<https://betterprogramming.pub/ldap-docker-image-with-populated-users-3a5b4d090aa4>

## 具有预定义对象资源的 Docker 图像

![](img/b8a05de75030b3200a5db700d61780f9.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在过去的几个月里，我一直在使用一些服务和应用程序，这些服务和应用程序通过 LDAP 服务器对用户进行身份验证。这意味着在我的本地机器上多次部署 LDAP 服务器，并且每次都填充用户。我需要部署 LDAP 服务器来登录并在我的开发环境中使用我的服务。

> LDAP 是一种轻量级目录访问协议，它是一种成熟、灵活、支持良好的基于标准的机制，用于与目录服务器进行交互。它通常用于鉴定和存储有关用户、组和应用程序的信息，但是 LDAP 目录服务器是一个相当通用的数据存储，可以用于各种各样的应用程序。
> 
> [https://ldap.com/](https://ldap.com/)

LDAP 协议有几种常用的实现，包括 ApacheDS、OpenLDAP、OpenDJ 和 Active Directory。

填充数据和用户是一个耗时的过程。它还取决于需要填充的数据量。但是，即使是非常简单的数据，也不得不多次手动做同样的事情，这促使我自动化这个过程，从而减少完成工作所需的工作量。

我开始寻找一种方法来帮助我用填充的数据部署 LDAP 服务器。经过一番挖掘，我发现 LDAP Docker 映像`[osixia/openldap](https://github.com/osixia/docker-openldap)`支持用现有的 LDAP 数据引导 LDAP 服务器。下面是使用 docker 映像`osixia/openldap`实现`LDAP`引导的步骤:

*   准备一个引导文件。创建一个引导文件`ldif`，其中包含启动后需要导入到 LDAP 服务器的所有数据。
*   在自定义 Docker 映像`/container/service/sldap/assets/config/bootstrap/ldif/`中的以下路径下添加引导文件。
*   建立 Docker 形象。
*   使用自定义 Docker 映像部署`LDAP`服务器。

[](/manage-github-permissions-with-ldap-7aa80d2bace7) [## 使用 LDAP 管理 GitHub 权限

### 将 LDAP 数据同步到 GitHub 帐户以管理用户访问权限

better 编程. pub](/manage-github-permissions-with-ldap-7aa80d2bace7) 

# **引导文件的内容**

引导文件的内容可以包括任何有效的 LDAP 对象，例如，`inetOrgPerson`、`organizationalUnit`、`groupOfUniqueNames`和`groupOfNames`。为了简单起见，我将使用`ldif`来说明我们如何定义 LDAP 用户和组。

因为我们将在 LDAP 服务器中拥有`Users`和`Groups`对象，所以将这些对象组织在不同的类别或单元中是有意义的。LDAP 为此提供了一个名为`organizationalUnit`的对象类型。

下面的代码片段将在 LDAP 服务器中创建两个组织单元——一个用于用户，一个用于组(如果您需要更多的单元，您可以复制下面的代码并修改`dn`和`ou`字段以满足您的需要)。

同样重要的是，必须将`changetype: add`作为每个条目的第二个属性，否则，引导过程将失败，并且`LDAP server`将关闭(这适用于引导文件中的所有条目)。

```
dn: ou=Groups,dc=shihadeh,dc=intern
changetype: add
objectclass: organizationalUnit
ou: Groupsdn: ou=Users,dc=shihadeh,dc=intern
changetype: add
objectclass: organizationalUnit
ou: Users
```

下一步是开始定义`LDAP`用户。下面的代码片段可用于定义 LDAP 用户—复制它并修改属性值以创建多个用户。用户`objectclass`应该保持不变(`inetOrgPerson`)对于所有创建的用户，其他字段可以根据您的需要进行更改。

```
dn: cn=developer,dc=shihadeh,dc=intern
changetype: add
objectclass: inetOrgPerson
cn: developer
givenname: developer
sn: Developer
displayname: Developer User
mail: [developer@gmail.com](mailto:developer@gmail.com)
userpassword: developer_pass
```

下一步是定义 LDAP 组并将用户分配给这些组。要定义组，我们可以使用 LDAP 对象类`groupsofUniqueNames`或对象类`groupsofNames`。下面的代码片段定义了两个 LDAP 组，并为每个组分配了不同的 LDAP 用户。

```
dn: cn=Admins,ou=Groups,dc=shihadeh,dc=intern
changetype: add
cn: Admins
objectclass: groupOfUniqueNames
uniqueMember: cn=admin_gh,dc=shihadeh,dc=interndn: cn=Maintaners,ou=Groups,dc=shihadeh,dc=intern
changetype: add
cn: Maintaners
objectclass: groupOfUniqueNames
uniqueMember: cn=maintainer,dc=shihadeh,dc=intern
uniqueMember: cn=developer,dc=shihadeh,dc=intern
```

生成引导文件的另一种方法是从 LDAP 用户界面创建`LDAP`对象，然后导出 LDAP 数据。一旦引导文件准备好了，我们需要将它保存在一个名为`bootstrap.ldif`的`ldif`文件中。完整的文件可能如下所示:

下一步是创建用于构建定制 Docker 映像的`Dockerfile`。最重要的操作是将引导文件复制到正确的路径。下面的 docker 文件可用于构建 LDAP 定制 Docker 映像，包括将在`LDAP`服务器启动时使用的引导文件。

```
FROM osixia/openldapLABEL maintainer="wshihadeh.devx@gmail.com"ENV LDAP_ORGANISATION="Al-waleed Test Org" \     LDAP_DOMAIN="shihadeh.intern"COPY bootstrap.ldif /container/service/slapd/assets/config/bootstrap/ldif/50-bootstrap.ldif
```

最后一步是部署 LDAP 服务器并测试是否已经创建了用户。我们可以通过使用命令`docker-compose up -d`部署下面的`docker-compose`文件来实现这一点。

部署服务后，LDAP 服务器将在以下 URL 上可用:`[http://127.0.0.1:389](http://127.0.0.1:389.)`。

此外，您将能够浏览 LDAP 服务器，查看其资源，并通过连接到以下 URL `[http://127.0.0.1:](http://127.0.0.1:389.)8090`创建新的资源。

# **结论**

使用填充的数据部署 LDAP 服务器是一个节省时间的好主意，特别是对于开发和测试环境。这可以通过向`LDAP` docker 映像添加一个包含所需数据的引导文件来实现。[在这里](https://github.com/wshihadeh/ldap_server)你可以找到完整的实现。