# 如何在 Vue.js 中使用自定义 HTML 作为拖动图像

> 原文：<https://betterprogramming.pub/how-to-use-custom-html-as-a-drag-image-with-vue-js-5b3d21c3c227>

## 你实际上不能真的这么做，但我们无论如何都要这么做

![](img/c7a3690dcee37afbd13ab6b5f544ec9b.png)

由 [DiceBear Avatars](http://avatars.dicebear.com) 提供的头像。

# 在开始之前

这种方法在 Chrome 上肯定管用，但在 Firefox 上不行。我还没有在 IE 或 Safari 上验证这一点。

如果你不熟悉 HTML 拖放 API 的基础知识，我强烈推荐你阅读 Mozilla 开发者文档。

# TL；DR——这是完整的例子

# 指定“假”拖动图像

文档说明你可以使用 DOM 上任何可见的*元素作为拖动图像。这破坏了我们的意图，因为我们希望元素只作为拖动图像可见。因此，创建一个假的空图像来取消默认行为。*

```
<div
  ref="fakeDragImage"
  class="fakeDragImage"
  style="display: none"
/>
```

# 创建“真实的”拖动图像

创建用于表示实际拖动图像的元素，并确保其可见性由拖动行为决定。

```
<div
  ref="realDragImage"
  v-show="isDragging"
  style="position: absolute; z-index: 1000"
><!-- Drag image info goes here --></div>
```

# 当 DragStart 事件被激发时

将`realDragImage`引用附加到父 Vue 元素。通常，这将是`#app`，但是您可以通过查看`src/main.js`文件来仔细检查。这样做的原因是，元素可以在页面上自由移动，而不必担心它最初被限制在 HTML 元素中的约束。

```
onDragStart(ev) {
  ...
  document.getElementById('app').append(this.$refs.realDragImage)
  ...
}
```

将`fakeDragImage`引用分配给事件上的`dataTransfer`对象。这样，当您初始化拖动时，就看不到默认的拖动图像。

```
onDragStart(ev) {
  ...
  ev.dataTransfer.setDragImage(this.$refs.fakeDragImage, 0, 0)
  ...
}
```

# 当拖动事件被激发时

根据`ev.pageX`和`ev.pageY`坐标设置`realDragImage`的位置，使元素可见。

```
onDrag(ev) {
  if (!ev.pageX && !ev.pageY) return const [offsetX, offsetY] = [4, 4]
  this.$refs.realDragImage.style.left = ev.pageX + offsetX + 'px'
  this.$refs.realDragImage.style.top = ev.pageY + offsetY + 'px'
  this.isDragging = true
}
```

有一个小问题:当拖动操作不成功时(即元素没有被放到任何有效的放置位置)，触发`drag`，使得`ev.pageX`和`ev.pageY`成为`(0, 0)`。这正好发生在`dragend`事件触发之前，所以图像会瞬间闪烁到页面的左上角。为了防止这种情况，只要在事件发出`(0, 0)`时返回即可。

# 触发 DragEnd 事件时

设置`this.isDragging=false`再次隐藏拖动元素。

仅此而已。这就是你如何破坏 HTML 拖放 API 来设置你自己的自定义拖动行为。感谢阅读！

# 资源

*   [DiceBear 头像](http://avatars.dicebear.com)
*   [HTML 拖放 API](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API)