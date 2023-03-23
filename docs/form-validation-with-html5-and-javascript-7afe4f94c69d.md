# 用 HTML5 和 JavaScript 进行表单验证

> 原文：<https://betterprogramming.pub/form-validation-with-html5-and-javascript-7afe4f94c69d>

## 验证，做得更好

![](img/9d0f516aad2319eb20b114bdbc2db5b7.png)

照片由 [Jonatan Pie](https://unsplash.com/@r3dmax?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

表单验证是浏览器端 HTML 和 JavaScript 的一部分。在将数据发送到服务器之前，我们可以用它来验证表单输入。然而，我们应该信任正在发送的内容，所以最终的验证应该仍然在服务器上。

在 HTML5 中，表单验证是一个内置特性。HTML5 有各种验证属性。

当表单输入有效时，`:valid` CSS 伪类被应用于元素。如果无效，那么`:invalid` CSS 伪类被应用到元素。

当表单有无效输入时，浏览器将阻止提交表单并显示一条错误消息。

# 表单验证属性

## 模式

`pattern`属性适用于类型为`text`、`search`、`url`、`tel`、`email`和`password`的输入元素。

*   它让我们设置一个正则表达式作为值，浏览器将根据它进行验证。

## 福建话

该属性适用于`range`、`number`、`date`、`month`、`week`、`datetime`、`datetime-local`和`time`输入。

*   当应用于`range`或`number`输入时，它将检查输入的数字是否为`min`属性的值或更高。
*   当它应用于`date`、`month`或`week`元素时，它将检查日期是否大于或等于该属性的值中给定的日期。
*   对于`datetime`、`datetime-local`、`time`类型的输入，检查日期和时间是否大于或等于`min`属性的值。

## 最大

`max`属性与`min`属性相反。它检查输入的值是否小于或等于该属性的值。

*   当应用于`range`或`number`输入时，它会检查输入的数字是否等于或小于`min`属性的值。
*   当它应用于`date`、`month`或`week`元素时，它将检查日期是否小于或等于该属性的值中给定的日期。
*   对于`datetime`、`datetime-local`、`time`类型的输入，检查日期和时间是否小于或等于`min`属性的值。

## 需要

`required`属性检查输入值是否由用户输入。

*   可用于`text`、`search`、`url`、`tel`、`email`、`password`、`date`、`datetime`、`datetime-local`、`month`、`week`、`time`、`number`、`checkbox`、`radio`、`file`以及`<select>`和`<textarea>`元素。

## 步骤

`step`属性检查输入是否为整数。

*   如果它应用于类型为`date`的输入元素，那么它检查整数天。
*   如果它应用于类型为`month`的输入元素，那么它检查整数个月。
*   如果它应用于类型为`week`的输入元素，那么它检查整数周数。
*   如果它应用于类型为`datetime`、`datetime-local`、`time`的输入元素，那么它检查整数秒。
*   如果它应用于类型为`range`、`number`的输入元素，那么它会检查一个整数。

## 最小长度

`minlength`属性适用于`text`、`search`、`url`、`tel`、`email`、`password`类型的输入。在`textarea`元素上也有。

*   它检查是否大于或等于用户输入的文本字符数。

## Maxlength

`maxlength`属性适用于`text`、`search`、`url`、`tel`、`email`、`password`类型的输入。它也可以在`textarea`元素上使用。

*   它检查小于或等于用户输入的文本字符数。

![](img/2d3918ff3e587635654ed2aa44909fd0.png)

由[浸礼会会员](https://unsplash.com/@baptiststandaert?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用表单验证属性

我们可以通过将表单验证属性添加到元素中来使用它们。例如，我们可以编写一个接受电子邮件地址作为输入的表单。

首先，我们编写如下的 HTML:

在上面的代码中，我们有带有`required`属性的`input`元素，它根据需要设置输入。

我们还添加了`pattern`属性，用电子邮件地址的正则表达式作为值。它检查 at 符号和句点前后的字符。

接下来，我们添加 CSS，用于在输入有效或无效时更改`input`元素的边框，如下所示:

我们使用了本文开头提到的伪类来完成这项工作。

最后，我们添加 JavaScript 代码，通过调用`preventDefault`来阻止表单提交:

```
const form = document.querySelector('#form');
form.onsubmit = (e) => {
  e.preventDefault();
}
```

另一个例子是检查我们的输入和范围的长度。例如，我们可以编写以下 HTML 来获取用户的姓名和年龄:

我们的输入元素分别具有名称和年龄的长度和范围属性。此外，我们有输入消息，当输入以消息中描述的方式无效时，我们可以看到它们。

同样，当输入有效或无效时，我们使用与第一个示例相同的 CSS 来更改输入框的边框:

最后，我们有在输入无效时显示验证消息的 JavaScript:

在上面的代码中，我们将`oninput`事件处理程序设置为一个事件处理程序函数，以便在输入内容时检查输入的有效性。

在每个函数中，我们首先隐藏所有的消息，这样我们就看不到过时的消息，然后检查名称输入的`tooShort`或`tooLong`，因为我们指定了最小和最大长度。

如果出现任何错误，我们在 HTML 中取消隐藏相应的消息元素。

同样，我们检查年龄输入的`rangeOverflow`或`rangeUnderflow`，因为我们指定了最小和最大长度。如果出现任何错误，我们在 HTML 中取消隐藏相应的消息元素。

使用 HTML5 和 JavaScript，我们可以检查各种输入值的有效性。我们不必使用任何库来做到这一点。

我们可以检查一些内容，包括长度、范围、任何带有正则表达式的模式等等。然而，我们还应该在保存之前检查服务器端，因为用户仍然可以入侵浏览器端的应用程序来跳过验证。