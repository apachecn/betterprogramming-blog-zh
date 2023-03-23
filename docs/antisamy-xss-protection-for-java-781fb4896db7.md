# 反垄断:Java 的 XSS 保护

> 原文：<https://betterprogramming.pub/antisamy-xss-protection-for-java-781fb4896db7>

## 为 Java 启用 XSS 保护

![](img/d84cffecdfa6329fce405a0579f0d3d8.png)

Camille Minouflet 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

跨站点脚本(XSS)允许攻击者向其他用户查看的网页中插入代码。恶意 web 用户可以利用此安全漏洞绕过访问控制。

当受害者访问执行恶意代码的网页或 web 应用程序时，就会发生真正的攻击。网页或 web 应用程序成为向用户浏览器传送恶意脚本的工具。

大多数时候，恶意脚本是通过

*   URL 参数
*   表单参数(GET 和 POST 参数)
*   饼干
*   HTTP 标题

为了缓解 XSS 脚本问题，应该对输入和输出数据进行转义和过滤，主要是输出。转义和过滤是至关重要的，转义任何发送到浏览器的输出数据。在开发和测试过程中，预防 XSS 是最重要的。

# 什么是反婚？

AntiSamy 是一个用于 Java 的 HTML、CSS 和 JavaScript 过滤器，它根据策略文件过滤用户输入。AntiSamy 不是 HTML、CSS 或 JavaScript 验证器。反别名仅仅是一种确保 HTML、CSS 和 JavaScript 输入严格遵循策略文件定义的规则的方法。它是一个 API，用于确保用户提供的 HTML/CSS 符合应用程序的规则

AntiSamy 是一个净化框架，用户决定如何进行净化。AntiSamy 不能保证过滤所有恶意代码。AntiSamy 只是做策略文件所描述的事情。

处理反艾米的基本步骤是

*   定义策略文件(XML)
*   根据策略清理用户输入/输出

# 反垄断政策文件

AntiSamy 使用一个 XML 策略文件来定义应用程序中允许哪些 HTML 标记和属性。

指令是控制反别名过滤过程的变量。

```
<directives>
   <directive name="omitXmlDeclaration" value="true" />
   <directive name="omitDoctypeDeclaration" value="true" />
   <directive name="maxInputSize" value="200000" />
   <directive name="useXHTML" value="true" />
   <directive name="formatOutput" value="true" />
   <directive name="nofollowAnchors" value="true" />
   <directive name="validateParamAsEmbed" value="true" />
    <directive name="embedStyleSheets" value="false" />
   <directive name="connectionTimeout" value="5000" />
   <directive name="maxStyleSheetImports" value="3" />
</directives>
```

omitxmlrdeclaration:当“omitxmlrdeclaration”打开时，AntiSamy 会自动将 XML 头添加到输出中。启用此功能将告诉 AntiSamy 不要这样做。

omitDoctypeDeclaration:当启用该特性时，AntiSamy 将自动在 HTML doctype 声明的前面加上前缀。默认情况下，AntiSamy 不会在此标题前添加内容。

maxInputSize:该指令指定用户输入在被验证之前的最大大小，以字节为单位。

useXHTML:当这个特性打开时，AntiSamy 将以 XHTML 格式输出净化后的数据，而不仅仅是普通的 HTML。

formatOutput:启用时，AntiSamy 会根据一些基本规则和缩进自动格式化输出。

nofollowAnchors:启用时，AntiSamy 会将 rel="nofollow "属性附加到输入中提供的所有 anchor ( )标记。

validateParamAsEmbed:启用时，AntiSamy 将把策略中的<param>标记的属性视为与嵌套在<embed>中的任何标记相同。

embedStyleSheets:显示用户输入中引用的远程样式表是否将被下拉并嵌入到当前用户输入中。

connectionTimeout:当启用了 embedStyleSheets 时，将在获取有问题的场外资源时使用超时值，以毫秒为单位计算。

maxStyleSheetImports:该特性允许开发人员指定可以从任何单个输入中下载多少个远程样式表。

公共正则表达式:您可以在这里声明正则表达式，以便在策略文件的其余部分使用。

<common-regexps></common-regexps>

```
<regexp name="htmlClass" value="[a-zA-Z0-9\s,\-_]+"/>
```

正则表达式用于确定 htmlClass 属性中的文本是否有效。

公共属性:这里声明了许多不同标签共有的属性。

<common-attributes></common-attributes>

```
<attribute name="class" description="The 'class' of any HTML attribute is usually a single word, but it can also be a list of class names separated by spaces">
 <regexp-list>
  <regexp name="htmlClass"/>
 </regexp-list>
</attribute>
```

全局标记属性:在这里声明对所有不同的标记都是全局的属性。

<global-tag-attributes></global-tag-attributes>

```
<attribute name="class"/>
```

要编码的标签:在这里声明不会被删除、过滤、验证或截断的标签，但是使用 HTML 实体进行编码。

<tags-to-encode></tags-to-encode>

```
<tags-to-encode>
 <tag>g</tag>
 <tag>grin</tag>
</tags-to-encode>
```

标记规则:在这里定义将分别用于每个标记的解析规则。这些定义如下:

*   移除:这个标签将移除整个标签，当它遇到
*   validate:该标记根据定义的规则验证标记属性
*   filter:这个标签过滤掉标签和子标签，并保留内容。
*   truncate:该标签删除内容和子内容，但保留标签。

```
<tag name="html" action="validate"/>
```

CSS 规则:我将在这里定义解析规则，这些规则将分别用于每个 CSS 属性

```
<property name="background-attachment" description="If a background image is specified, this property specifies whether it is fixed with regard to the viewport ('fixed') or scrolls along with the document ('scroll').">
   <literal-list>
      <literal value="scroll" />
      <literal value="fixed" />
      <literal value="inherit" />
   </literal-list>
</property>
```

Refer to the [来自 GitHub 的反萨米策略文件示例](https://github.com/andresriancho/owaspantisamy/tree/master/Java/antisamy-sample-configs/src/main/resources)。如果默认策略文件不能满足您的需要，您可以根据项目需要自定义策略文件。

# 输出净化

创建一个返回简单 HTML 的 servlet，并基于 GitHub 上的默认规则[启用反别名验证。](https://github.com/andresriancho/owaspantisamy/blob/master/Java/antisamy-sample-configs/src/main/resources/antisamy-slashdot.xml)

已添加以下依赖项:

*   安赛蜜-1.5.10.jar
*   蜡染 css-1.7.jar
*   nekohtml-1.9.21.jar
*   xercesImpl-2.12.0.jar

HTML 输出在 Servlet 中有一个属性为“test”的锚元素

```
<a **test=\"test\"** href=\"http://www.test.com/test.html\">Link</a>
```

“test”属性在默认策略下对 anchor 元素无效，并被 AntiSamy getCleanHTML()方法过滤。

```
CleanResults cr=new AntiSamy().scan(html, getPolicy());cr.getCleanHTML();
```

输出的 HTML 没有测试属性。

```
<html>
 <head><title>Hello</title></head>
 <body> <br/> <br/>
 ** <a href="**[**http://www.test.com/test.html**](http://www.test.com/test.html)**">Link</a>**
 </body>
</html>
```

现在让我们更改策略文件，以接受锚元素的测试属性。

用 regex 为支持的属性值定义一个新的属性测试。

```
<attribute name="test">
 <regexp-list>
  <regexp value="[a-zA-Z0-9]+"/>
 </regexp-list>
</attribute>
```

启用锚标记的“测试”属性。

```
<tag name="a" action="validate">
 <attribute name="href" onInvalid="filterTag"/>
 **<attribute name="test"/>**

 <attribute name="nohref">
  <literal-list>
   <literal value="nohref"/>
   <literal value=""/>
  </literal-list>
 </attribute>
 <attribute name="rel">
  <literal-list>
   <literal value="nofollow"/>
  </literal-list>
 </attribute>
</tag>
```

现在，输出 HTML 将具有锚元素的 test 属性。

```
<html>
 <head><title>Hello</title></head>
 <body> <br/> <br/>
  **<a href="**[**http://www.test.com/test.html**](http://www.test.com/test.html)**" test="test">Link</a>**
 </body>
</html>
```

可以根据项目需求修改策略文件来限制或允许 HTML 数据，甚至可以定义一个 Java 过滤器来过滤传入的请求参数。

# **演示网络项目**

[](https://github.com/techforum-repo/youttubedata/tree/master/AntiSamyDemo) [## 技术论坛-repo/YouTube data

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/techforum-repo/youttubedata/tree/master/AntiSamyDemo) 

[anti Samy](https://www.wavemaker.com/learn/app-development/app-security/xss-antisamy-policy-configuration)库有助于净化 HTML 以减轻 Java 中的 XSS 攻击。让我们在下一篇教程中讨论另一个名为 [Owasp-java-html-sanitize](https://owasp.org/www-project-antisamy/) 的库，它有助于净化 html 数据。