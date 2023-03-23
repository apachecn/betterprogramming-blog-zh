# 什么时候使用 Angular ControlValueAccessor，没有它有什么区别？

> 原文：<https://betterprogramming.pub/angular-with-controlvalueaccessor-and-without-less-boilerplate-da27f0a16639>

## 角形

## 带 ControlValueAccessor 和不带 less 样板文件的 Angular。

![](img/6c4d2c512a0869f9403b36d3d2432466.png)

今天我们来研究一下`ControlValueAccessor`的用法，什么时候应该使用，什么时候是多余的。

`ControlValueAccessor`提供了将`FormControl`用于任何实现该接口的组件的能力。这提供了很大的开发灵活性，是`Angular Forms`中的一个有用特性。

有几种方法可以用样板文件来控制装饰和使用。我们来看一下，用一个例子找出不同之处。

## 例子

假设我们需要创建一个计数器组件，并通过`FormControl`使用它的值。

带`ControlValueAccessor`的计数器组件代码:

很多代码，是吗？

用在这种情况下太复杂了。什么更好——用`ControlValueAccessor`样板文件创建一个抽象类并继承它。但这个时候其实没那个必要。

我们能不能简化组件？当然可以:

非常简单的组件。但是这种简单性给我们带来了什么限制呢？

*   首先，我们要直接传递控制，而不是用`FormControlName`或`ngModel`:

*   第二，我们失去了模板验证器，不能像这样使用它:

## 丰富

我们怎样才能让我们的代码变得更好？

让我们将`formControl`直接传递给`@Input`用作角度指令。但是，如果我们这样做，将会出现一个错误，错误:没有带有未指定名称属性的表单控件的值访问器。这个错误可以用另一个指令`ngDefaultControl`修复:

现在它可以很好地与模板验证器一起工作，并且没有错误。

另一个技巧是，如果控件在`FormGroup`中使用，我们必须用函数传递控件。但是不要这样，只要使用一个纯的`pipe`就可以了，每次`ChangeDetection`通过都不需要重新计算。例如:

最后，我们的反控制变成了:

希望这篇文章对你有帮助。

谢了。