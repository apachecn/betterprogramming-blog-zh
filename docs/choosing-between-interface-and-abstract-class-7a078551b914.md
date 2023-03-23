# 在接口和抽象类之间选择

> 原文：<https://betterprogramming.pub/choosing-between-interface-and-abstract-class-7a078551b914>

## 他们完成相同的结果；这取决于你想做什么

![](img/38e7149794c990d1899160d3c747e125.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在一些采访中，我被要求解释接口和抽象类之间的区别，以及什么时候我会选择一个而不是另一个。

因为这种情况发生得太多了，所以我将定义每一个并解释两者之间的区别，并给出一个何时选择一个而不是另一个的例子。

我将使用 Java 编写示例代码，但是这个概念在其他语言中也应该是类似的。

# 什么是接口？

接口是多个系统之间的行为契约。

这意味着任何实现一个接口的类保证并且*必须*为它的所有方法提供一些实现。接口中的所有方法都必须是公共的和抽象的。

接口定义如下所示:

```
public interface Movable {
    public void accelerate();
    public void decelerate();
}public interface Drivable{
    public void drive();
}
```

# 什么是抽象类？

抽象类在其声明中以 abstract 关键字为前缀，是为其派生的具体类创建的准则。抽象类必须至少有一个抽象方法，并为其非抽象方法提供实现。

如果你用实现来定义一个抽象类，那么你可能需要考虑使用接口是否是一个更好的选择。

抽象类定义如下所示:

```
public abstract class Vehicle {

    public void getReady(){
        System.*out*.println("I'm all set and ready to go");
    }
    public abstract void start();
    public abstract void stop(); 
}
```

为了将所有这些放在一起，让我们创建一个`Bus`类，它扩展了`Vehicle`抽象类并实现了`Drivable`和`Movable`接口。

```
public class Bus extends Vehicle implements Drivable, Movable { @Override
    public void Drive() {
        System.*out*.println("Driving...");
    } @Override
    public void accelerate() {
        System.*out*.println("accelerating...");
    } @Override
    public void decelerate() {
        System.*out*.println("decelerating...");
    } @Override
    public void start() {
        System.*out*.println("Starting engine...");
    } @Override
    public void stop() {
        System.*out*.println("Shutting down engine...");
    }
}
```

# 差异和相似之处

接口中的所有方法都是公共的，并且是隐式抽象的。抽象类可以对它们的方法使用公共的、部分的、受保护的和静态的访问修饰符。

类可以实现多个接口，但只能实现一个抽象类。抽象类可以包含非抽象方法。

它们都可以有方法和变量，但都不能被实例化。接口中声明的所有变量都是 final，而抽象类可能包含非 final 变量。

# 选择哪一个

如果你需要提供一些实现或者需要一个基类，那么抽象类是一个不错的选择。

例如，也许你想确保在一个类中初始化一些变量，以便在一个所有派生类都可以使用的 helper 方法中执行逻辑，选择一个抽象类是正确的选择。

如果你需要为你的类提供额外的行为，那么接口是一个不错的选择。

两者可以实现相同的功能，然而，当考虑编码标准时，接口可以帮助你实现抽象和多态，这是四个主要 OOP 原则中的两个。

它还使您的代码更容易保持松散耦合，而不是紧密耦合，当高级模块依赖于低级模块时会发生这种情况。

接口也用于依赖注入(也称为 DI/IoC ),并且在测试时更容易模仿派生类。

感谢您的阅读。编码快乐！