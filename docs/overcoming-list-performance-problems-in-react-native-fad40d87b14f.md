# 克服“反应自然”中的列表性能问题

> 原文：<https://betterprogramming.pub/overcoming-list-performance-problems-in-react-native-fad40d87b14f>

## 用我自己的定制 NPM 套餐

![](img/50e42a8a085b9a505bdf8d15cbbc12a7.png)

在 React Native 中处理复杂的屏幕时，很容易出现性能问题。大列表可能是性能问题最常见的原因，甚至 FlatList 组件也不能平滑地处理包含成千上万项的列表。

有一些库可以帮助处理大的列表，它们有很好的性能。例如:

*   [https://www.npmjs.com/package/react-native-big-list](https://www.npmjs.com/package/react-native-big-list)
*   [https://www.npmjs.com/package/react-native-largelist](https://www.npmjs.com/package/react-native-largelist)
*   [https://www.npmjs.com/package/recyclerlistview](https://www.npmjs.com/package/recyclerlistview)

但是在我上一个项目中尝试了这些选项后，面对一些问题，我决定研究一个新的解决方案，叫做[快速列表](https://www.npmjs.com/package/react-native-speedy-list)。

# 快速列表组件

[快速列表](https://www.npmjs.com/package/react-native-speedy-list)背后的想法实际上与`[RecyclerListView](https://www.npmjs.com/package/recyclerlistview)`相同，一旦用户向下滚动列表，它会重用列表中的现有项目来呈现新项目。

假设您的屏幕高度为 1920 像素，每个列表元素的高度为 192 像素，因此 20 个项目就足以容纳两倍于整个屏幕的大小。一旦您开始滚动列表，第一项将隐藏在顶部，用户看不到。[快速列表](https://www.npmjs.com/package/react-native-speedy-list)将利用这一点，将该隐藏项目重新定位在位置 21，并将其内容更改为项目 21 的内容，这比每次滚动屏幕时呈现新组件更快。

另一个很好的改进是在第一次渲染时。代替一次渲染 20 个元素，Speedy List 将以可配置的批次数量渲染这些元素，这使得导航更加柔和。

# 安装和使用

您可以使用下面的命令将[快速列表](https://www.npmjs.com/package/react-native-speedy-list)添加到您的反应原生项目中:

```
yarn add react-native-speedy-list 
```

或者

```
npm install --save react-native-speedy-list
```

下面你可以看到一个[快速列表](https://www.npmjs.com/package/react-native-speedy-list)组件用法的小例子:

> 这个例子是用 TypeScript 写的，所以你可能会认为这个接口**用户**是在某处声明的，其他类型来自 Speedy List。

像任何其他列表组件一样， [Speedy List](https://www.npmjs.com/package/react-native-speedy-list) 需要一个要呈现的项目列表，一个呈现方法，以及唯一标识每个项目的方法。除了这些通常的属性，列表还需要通过属性`itemHeight`知道每个项目的高度，这有助于布局计算和回收周期。

要记住的一个重要细节是，这个组件的所有性能都来自于回收已经渲染的组件的能力，所以你必须尝试在项目渲染器方法中始终保持相同的组件树，并且还要避免使用 key prop，因为它已经由 [Speedy List](https://www.npmjs.com/package/react-native-speedy-list) 管理。

# 动态项目高度

固定的项目高度可以很好地处理简单列表，并带来很好的性能。

但是对于更复杂的布局，您可能需要根据一些逻辑来设置每个项目的高度。通过使用函数而不是静态整数，可以很容易地将其存档，如下所示:

注意，也可以通过函数动态设置 item key 属性。

# 高级道具

[**极速清单**](https://www.npmjs.com/package/react-native-speedy-list) 还有一套有趣的道具来控制回收过程:

```
// Used to detect when an item changes
itemEquals (a: T, b: T) => boolean// Batch size for the very first render. 
// A small value may show your screen layout faster, 
// but the user might see the items being rendered.
initialBatchSize: number// Amount of recyclable items to render. 
// Try to use a value greater enough to fill the screen height.
recyclableItemsCount: number// Interval in milliseconds between list updates.
recyclingDelay: number
```

尝试这些值，找到最适合您需求的配置。您可以在这里查看所有可用的房产:[https://github.com/FSPinho/react-native-speedy-list](https://github.com/FSPinho/react-native-speedy-list)

# 结论

这个组件帮助我克服了 React 本地项目中最大的性能问题，我希望它能满足您的需求。

我很乐意阅读您的意见和建议，请随时联系我们！