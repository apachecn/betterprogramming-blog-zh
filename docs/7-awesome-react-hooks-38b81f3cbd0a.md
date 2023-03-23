# 7 个很棒的反应钩

> 原文：<https://betterprogramming.pub/7-awesome-react-hooks-38b81f3cbd0a>

## React 挂钩，你可以每天使用，以提高你的生产力

![](img/09cd4ff331c24ca2a584bfd18148d2ba.png)

詹姆斯·夸尔特洛夫在 [Unsplash](https://unsplash.com/s/photos/hook?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片。

在 [React Hooks](https://reactjs.org/docs/hooks-intro.html) 发布之前，框架的功能组件在使用上有点受限。当涉及到使用状态、上下文 API 和一些生命周期方法时，我们无能为力。

但是从 React 16.8 开始，我们可以更加灵活地重用现有的代码。

今天，你将学习如何使用不同的 React 挂钩来解决日常问题。那么，让我们开始这个有趣的旅程吧。

# 使用文件挂钩

克里斯托弗·帕蒂收集了一批非常酷的被称为骗子的钩子。

我个人喜欢`useFiler`钩子，因为它使我们能够在 web 浏览器中创建虚拟文件系统。基本上，它利用浏览器的本地存储来管理文件及其内容。

首先，在您的应用程序中安装 crooks 包:

```
npm install crooks --save
```

现在，从 crooks 导入`useFiler`钩子:

```
import { useFiler } from 'crooks'
```

此时，我们已经准备好初始化钩子并管理一个虚拟文件系统。下面是一个简短的示例代码片段:

从上面的代码中可以看出，我们现在可以访问`add()`、`remove()`、`update()`和`clear()`方法。让我们来看看如何使用它们。

## 添加文件

`add()`函数接受一个必需的参数。这里，我们需要传递 JSON 可序列化的数据:

```
add("Save this JSON-serializable data in the file.")
```

注意:这个函数会自动为每个新文件生成一个 ID，但是你仍然可以通过传递一个整数或者字符串作为第二个参数来设置一个自定义的 ID。

## 更新文件

`update()`方法接受两个参数。第一个参数是文件的 ID，而第二个参数用于传递新数据:

```
update("abc1234", "New content of file.")
```

## 删除文件

在`remove()`函数中传递文件的 ID 来删除它:

```
remove("abc1234")
```

## 清除所有文件

调用`clear()`函数删除所有文件:

```
clear()
```

# 使用提取挂钩

史蒂文·波斯(MERN 堆栈开发人员)汇编了一份名为“钩子队长”的 React 钩子清单。他们在日常工作中很有帮助。

接下来的几个钩子的例子将来自他的收藏。

`useFetch`可以用来从一个 API 获取数据。请求完成后，它将返回响应和错误(如果有)。

将其导入到您的项目中:

```
import useFetch from "hooks/useFetch";
```

提出请求:

```
const { response, errors } = useFetch("https://api.github.com/users/torvalds/repos");
```

# 使用悬停钩

`useHover` 钩属于船长钩系列。

基本上，它跟踪屏幕上的鼠标光标来检测它是否放在特定的元素上。如果是，将触发悬停事件。

导入挂钩:

```
import useHover from "hooks/useHover";
```

初始化`useHover`钩子:

```
const [hoverMe, isHovered] = useHover();
```

这里，`hoverMe`指的是特定的 HTML 元素，而`isHovered`包含一个可以在条件语句中检查的布尔值。

例如，我们可以这样使用:

```
<div ref={hoverMe}>{isHovered ? "Hovered!" : "Hover me!"}</div>
```

# 使用废料钩

slug 是每个 web 项目的重要组成部分。其实还能提振一个网站的 SEO。

这就是为什么史蒂文在他的虎克船长系列中加入了`useSlug`。我们可以用它快速地将任何字符串转换成 SEO 友好的 slug。它足够聪明，可以用它的标准对应物替换任何发音符号(重音符号)。

例如，它会将 *é* 或 *è* 转换为 *e* 。

像往常一样，我们首先需要导入钩子:

```
import useSlug from "hooks/useSlug";
```

## **用法**

初始化钩子时，将任何字符串(例如文章标题)作为第一个参数传递。因此，它将返回一个可以立即在项目中使用的格式良好的段塞:

```
useSlug("React Hooks! résoudre les problèmes quotidiens");// react-hooks-resoudre-les-problemes-quotidiens
```

# 使用拉钩和吊钩

有一个开源的 React Hooks 库叫做 [ahooks](https://github.com/alibaba/hooks) 。它是由电子商务巨头阿里巴巴和一些志愿者共同开发的。

在写这篇文章的时候，它有大约 46 个钩子。他们每个人都专注于解决一个特定的问题。

这一节，我给大家介绍一对钩子:`useDrag`和`useDrop`。到目前为止，您可能已经对他们的工作有所了解。不过，我不得不提到，它们帮助我们实现了 HTML5 的拖放功能。

安装:

```
npm install ahooks --save
```

导入挂钩:

```
import { useDrag, useDrop } from 'ahooks';
```

## **用法**

首先初始化`useDrag`和`useDrop`钩子。

返回传递给 DOM 元素的属性。`useDrop`返回传递到放置区域的道具。它还使用一个布尔属性(`isHovering`)通知我们拖动元素是否在拖放区域的顶部。

最后，`useDrop`有四个回调函数，它们是根据被丢弃的条目的类型来执行的:

*   `onText`
*   `onFiles`
*   `onUri`
*   `onDom`

可以用鼠标拖动的 HTML5 元素:

```
<div {...getDragProps(id)}>Draggable Element</div>
```

一个 HTML5 元素，您可以在其中放置一些内容:

```
<div {...props}>
  {isHovering ? 'Release Item Here' : 'Drop Anything Here'}
</div>
```

# 使用标记模式挂钩

Craig Walker 最初开发了 [React 食谱](https://github.com/craig1123/react-recipes)，这是一个流行的定制 React 挂钩系列。

它提供了`useDarkMode`钩子来切换网站主题的明暗模式。切换模式后，它将当前值存储在`localStorage`中。这意味着用户的首选模式将立即应用于所有打开我们网站的浏览器标签。

安装库:

```
npm install react-recipes --save
```

导入:

```
import { useDarkMode } from "react-recipes";
```

## **快速示例**

基本上，`useDarkMode()`返回两个东西。

*   `darkMode`:当黑暗模式激活时为真的布尔值。
*   `setDarkMode`:在亮暗模式之间切换。

# 结论

今天，你已经学会了 React 挂钩在日常生活中的用法。我们刚刚看到了一些用例，但是有大量的开源钩子可以包含在您的项目中。

现在，我希望你在使用钩子的 React 项目中使用别人的代码时不会感到不舒服。事实上，React Hooks 的主要好处是它使我们能够编写更好的可重用代码。

感谢阅读！