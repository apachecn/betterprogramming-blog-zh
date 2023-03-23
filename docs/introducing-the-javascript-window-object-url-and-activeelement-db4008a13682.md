# JavaScript 窗口对象简介

> 原文：<https://betterprogramming.pub/introducing-the-javascript-window-object-url-and-activeelement-db4008a13682>

## 浏览 URL、activeElement 和 fullscreenElement 属性

![](img/4e736a3624865ffc120adba6dbfb9fbb.png)

马修·T·雷德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

`window`对象是一个全局对象，它具有与当前 DOM 文档相关的属性，这些属性是浏览器选项卡中的内容。

因为`window`对象是全局的，所以它在应用程序的每个部分都是可用的。

在这一部分中，我们将探索`URL`、`activeElement`和`fullscreenElement`属性。

# window.document.URL

我们可以使用`URL`属性以字符串形式获取当前加载页面的 URL。这是一个只读属性。例如，我们可以写:

```
console.log(window.document.URL);
```

那么我们应该得到类似 https://fiddle.jshell.net/_display/的东西。

# windows . document . active element

`activeElement`属性是一个只读属性，它返回处于焦点的`Element`对象。如果当时有文本选择，`activeElement`通常会返回一个`input`或一个`textarea`对象。

如果是这种情况，我们可以通过`selectionStart`和`selectionEnd`属性获得更多细节。聚焦元素也可以是类型为`button`、`checkbox`或`radio`的`select`元素或`input`元素。

用户可以按 tab 键将焦点移动到不同于他们当前聚焦的元素。空格键可以激活输入元素，如果它涉及到选择，如复选框或单选按钮。

对于 macOS 系统，默认情况下，不是输入的元素是不可聚焦的。例如，如果我们有以下 HTML 标记:

```
<form>
  <textarea name="textarea-1" id="textarea-1" rows="7" cols="40">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi interdum justo vel nulla laoreet mollis. Curabitur eget turpis id mi venenatis efficitur vestibulum sit amet libero. Fusce non varius tellus. Vestibulum a nisi venenatis, accumsan diam eget, aliquet ex. Sed tristique purus at est pulvinar, eu tincidunt justo vestibulum. Mauris fringilla interdum ipsum sed blandit. Suspendisse sollicitudin ut mi eget tincidunt. Sed et dolor diam. Praesent venenatis ipsum a libero interdum, in sagittis tellus elementum. Pellentesque in est vitae dui efficitur lacinia eget porta diam. </textarea> <textarea name="textarea-2" id="textarea-2" rows="7" cols="40">Duis urna dolor, fringilla quis dui quis, bibendum ornare ligula. Fusce ut nisi eu orci elementum rhoncus. Morbi ut tortor mauris. Morbi pretium tempus erat vitae dignissim. Duis et dolor vel metus tempor laoreet nec eget est. Curabitur tincidunt ullamcorper ante, sit amet iaculis arcu condimentum vitae. Donec tincidunt convallis maximus. Mauris pulvinar sem velit, congue volutpat nisl commodo sit amet. Duis ac est eu orci venenatis lacinia. </textarea>
</form>
```

和下面的 JavaScript 代码:

```
const textarea1 = document.getElementById('textarea-1');
const textarea2 = document.getElementById('textarea-2');
const onMouseUp = () => {
  console.log(document.activeElement);
}
textarea1.addEventListener('mouseup', onMouseUp, false);
textarea2.addEventListener('mouseup', onMouseUp, false);
```

然后，当我们单击两个`textarea`元素中的任何一个时，我们会看到`activeElement`是当前处于焦点的`textarea`元素。这是光标被单击后当前所在的位置。由于`activeElement`是一个`Element`对象，我们可以记录它的属性。我们可以将代码改为如下，以获得当前聚焦的`textarea`元素的`id`和`value`:

```
const textarea1 = document.getElementById('textarea-1');
const textarea2 = document.getElementById('textarea-2');
const onMouseUp = () => {
  const {
    id,
    value
  } = document.activeElement
  console.log(id);
  console.log(value);
}
textarea1.addEventListener('mouseup', onMouseUp, false);
textarea2.addEventListener('mouseup', onMouseUp, false);
```

当我们点击第一个`textarea`时，我们会看到:

```
textarea-1Lorem ipsum dolor sit amet, consectetur adipiscing elit. Morbi interdum justo vel nulla laoreet mollis. Curabitur eget turpis id mi venenatis efficitur vestibulum sit amet libero. Fusce non varius tellus. Vestibulum a nisi venenatis, accumsan diam eget, aliquet ex. Sed tristique purus at est pulvinar, eu tincidunt justo vestibulum. Mauris fringilla interdum ipsum sed blandit. Suspendisse sollicitudin ut mi eget tincidunt. Sed et dolor diam. Praesent venenatis ipsum a libero interdum, in sagittis tellus elementum. Pellentesque in est vitae dui efficitur lacinia eget porta diam.
```

然后，当我们点击第二个`textarea`，我们得到这个:

```
textarea-2Duis urna dolor, fringilla quis dui quis, bibendum ornare ligula. Fusce ut nisi eu orci elementum rhoncus. Morbi ut tortor mauris. Morbi pretium tempus erat vitae dignissim. Duis et dolor vel metus tempor laoreet nec eget est. Curabitur tincidunt ullamcorper ante, sit amet iaculis arcu condimentum vitae. Donec tincidunt convallis maximus. Mauris pulvinar sem velit, congue volutpat nisl commodo sit amet. Duis ac est eu orci venenatis lacinia.
```

同样，我们可以用单选按钮来做这件事。如果我们有下面的 HTML 代码:

```
<form>
  <input type='radio' id='choice1' name='choice'> Choice 1
  <input type='radio' id='choice2' name='choice'> Choice 2
</form>
```

然后我们稍微修改一下 JavaScript 代码，如下所示:

```
const choice1 = document.getElementById('choice1');
const choice2 = document.getElementById('choice2');
const onMouseUp = () => {
  const {
    id,
    value
  } = document.activeElement
  console.log(id);
  console.log(value);
}
choice1.addEventListener('mouseup', onMouseUp, false);
choice2.addEventListener('mouseup', onMouseUp, false);
```

然后我们得到被点击的单选按钮的 ID 和被选择的单选按钮的值——应该总是为`'on'`,因为它是被选择的单选按钮。对于复选框，我们可以编写以下 HTML 代码:

```
<form>
  <input type='checkbox' id='choice1' name='choice'> Choice 1
  <input type='checkbox' id='choice2' name='choice'> Choice 2
</form>
```

然后我们编写以下 JavaScript:

```
const choice1 = document.getElementById('choice1');
const choice2 = document.getElementById('choice2');
const onClick = () => {
  const {
    id,
    checked
  } = document.activeElement
  console.log(id);
  console.log(checked);
}
choice1.addEventListener('click', onClick, false);
choice2.addEventListener('click', onClick, false);
```

在上面的代码中，我们切换到监听`click`事件，以获得当前焦点`activeElement`的正确`checked`值。我们从获取`value`属性切换到获取`checked`属性，以便为复选框获取正确的值。

![](img/51d5b75a1ffa961c0ead34ac96bc4519.png)

照片由[威廉·艾文](https://unsplash.com/@firmbee?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# window . document . fullscreenelement

`fullscreenElement`是一个只读属性，它返回当前文档中当前全屏显示的`Element`对象，或者如果当前没有使用全屏模式，则返回`null`。

它可以用来获取对其进行了某些操作的元素，以使浏览器全屏显示。例如，如果我们有以下 HTML:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Full Screen</title>
  </head>
  <body>
    <img
      src="[https://images.unsplash.com/photo-1537498425277-c283d32ef9db?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=757&q=80](https://images.unsplash.com/photo-1537498425277-c283d32ef9db?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=757&q=80)"
      id="img"
    />
    <script src="main.js"></script>
  </body>
</html>
```

然后将以下 JavaScript 代码添加到`main.js`:

```
const img = document.getElementById("img");
img.addEventListener("click", async () => {
  await img.requestFullscreen();
  console.log(window.document.fullscreenElement);
});
```

然后，当我们单击 HTML 页面中的`img`元素时，将触发`click`事件，该事件将运行我们作为第二个参数传递给`img.addEventListener`方法的`click`处理程序。

我们在`img`对象上调用的`requestFullScreen`——HTML 中的`img`元素的 DOM 对象，将使浏览器选项卡全屏显示。

这将解析承诺，然后`console.log`将记录`window.document.fullscreenElement`。它应该记录`img`元素，我们单击该元素使浏览器全屏显示，因为现在`img`元素在被单击后以全屏模式显示。

不能保证全屏模式操作会成功。

如果全屏的许可被拒绝，那么由`requestFullScreen`方法返回的承诺将被拒绝，一个`fullscreenerror`事件将被触发。

`URL`、`activeElement`和`fullscreenElement`属性非常有用。属性让我们以字符串的形式获取当前加载页面的 URL。

为了获得当前聚焦的元素，我们可以使用`activeElement`属性。我们得到当前处于焦点的`input`、`textarea`或`select`元素。使用`fullscreenElement`我们可以获得以全屏模式显示的 DOM 元素。

这是当浏览器处于全屏模式时显示的 UI 元素。

例如，如果我们单击一个图像使浏览器全屏显示，那么`fullscreenElement`就是我们单击的图像，因为它现在以全屏模式显示。