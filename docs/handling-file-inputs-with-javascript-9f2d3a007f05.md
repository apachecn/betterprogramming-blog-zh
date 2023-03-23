# 如何用 JavaScript 处理文件输入

> 原文：<https://betterprogramming.pub/handling-file-inputs-with-javascript-9f2d3a007f05>

## 接受用户上传的内容

![](img/14961cf2e08440418b9d7c72cea9e3a8.png)

米歇尔·帕兹在 [Unsplash](https://unsplash.com/s/photos/elevator?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

许多 web 应用程序需要文件输入来处理前端的文件或将它们上传到后端。

在本文中，我们将看看如何添加一个文件输入，然后用 JavaScript 处理输入的文件。

# 访问选定的文件

给定以下 HTML，我们可以从文件输入中访问文件，如下所示:

```
<input type="file" id="input">
```

然后，我们可以通过编写以下内容来获取选择的文件:

```
const fileInput = document.getElementById('input');
fileInput.onchange = () => {
  const selectedFile = fileInput.files[0];
  console.log(selectedFile);
}
```

在上面的代码中，我们用事件处理程序监听了文件输入的 change 事件。然后在事件处理程序中，我们通过访问`files`属性来获取文件，这个属性是一个对象，第一个文件的键为 0。

# 处理多个文件

我们可以通过编写下面的代码来创建一个选择多个文件的文件输入。在 HTML 中，我们写道:

```
<input type="file" multiple id="input">
```

然后在 JavaScript 代码中，我们编写以下内容:

```
const fileInput = document.getElementById('input');
fileInput.onchange = () => {
  const selectedFiles = [...fileInput.files];
  console.log(selectedFiles);
}
```

第一个例子和第二个例子的区别在于，我们向文件输入添加了`multiple`属性。

然后在`onchange`处理程序中，我们用 spread 操作符将`files`对象转换成一个数组，以便更容易地操作项目。

`selectedFile`应该有一个文件对象数组。

# 动态添加更改监听器

我们还可以使用`addEventListener`将同一个监听器附加到文件输入，如下所示:

```
const fileInput = document.getElementById('input');const handleFiles = () => {
  const selectedFiles = [...fileInput.files];
  console.log(selectedFiles);
}fileInput.addEventListener("change", handleFiles);
```

# 获取关于所选文件的信息

我们可以从一个文件中获取各种属性来获得关于它们的信息。

例如，我们可以遍历如下选择的文件:

```
const fileInput = document.getElementById('input');fileInput.onchange = () => {
  const selectedFiles = [...fileInput.files];
  for (const f of selectedFiles) {
    console.log(f);
  }
}
```

文件对象中包含的一些属性包括:

*   `name` —只读字符串形式的文件名。这只是文件名，没有路径信息。
*   `size` —文件的大小，为只读的 64 位整数
*   `type` —文件的 MIME 类型，为只读字符串，如果类型无法确定，则为空字符串

例如，给定以下 HTML，我们可以如下所示显示上传文件的文件大小:

```
<input type="file" multiple id="input">
<p></p>
```

我们可以通过访问每个 file 对象的`size`属性来显示每个文件的文件大小，如下所示:

```
const fileInput = document.getElementById('input');
const p = document.querySelector('p');fileInput.onchange = () => {
  const selectedFiles = [...fileInput.files];
  p.textContent = selectedFiles.map(s => s.size).join(', ');
}
```

在 file 对象中可以使用`size`属性。

# 通过 click()方法使用隐藏的文件输入元素

我们可以用文件输入对象的`click`方法触发一个文件选择对话框打开。

然后，我们可以隐藏文件输入，并使用一些其他元素来打开文件输入选择对话框。

例如，我们可以通过编写以下 HTML 来实现这一点:

```
<input type="file" multiple id="input" style='display:none;'>
<button>Upload File</button>
```

然后，我们可以添加以下 JavaScript 来单击文件输入，以打开文件选择对话框，并听取文件输入的更改，如下所示:

前面的例子和这个例子之间的唯一区别是，我们添加了一个按钮元素，当用:

```
const button = document.querySelector('button');button.onclick = () => {
  fileInput.click();
}
```

文件输入侦听器的工作方式与其他示例相同。

# 使用拖放选择文件

我们还可以通过添加拖放侦听器，将文件拖动到一个框中来选择文件。

要创建一个文件拖放区 div，我们可以编写以下 HTML:

```
<div id='dropbox'></div>
```

然后我们可以把它放大，用下面的 CSS 添加一个背景色:

```
#dropbox {
  width: 500px;
  height: 500px;
  background-color: green;
}
```

然后在 JavaScript 代码中，我们可以添加以下拖放事件处理程序来处理文件放置，如下所示:

`dragenter`和`dragover`处理程序只是为了阻止默认动作和停止事件传播。

`drop`处理程序具有获取被丢弃文件的逻辑。`e.dataTransfer`属性有`files`属性，里面有文件。它是一个类似数组的对象，所以我们可以把它展开成一个数组。

# 结论

我们可以通过将输入的类型设置为`file`来获得带有文件输入的文件。此外，我们可以使用 drop listener 来获取放入元素中的文件。

`files`对象是一个类似数组的对象，拥有文件数据，包括名称、大小和类型。