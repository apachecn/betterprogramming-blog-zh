# 从 Java 中的方法返回集合

> 原文：<https://betterprogramming.pub/returning-collections-from-methods-in-java-33e0795df46e>

## 永远不要返回 null 来代替集合

![](img/a474d69dea5d91f54dbee90c13cb75e9.png)

图片由 [Ella Olsson](https://unsplash.com/@ellaolsson?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

编写软件通常意味着获取和返回数据。在像 Java 这样的面向对象语言中，我们通常用类的属性来表示数据。与许多新的面向对象语言不同，Java 不提供属性作为一级结构，所以我们使用 JavaBean getters 和 setters 来维护封装。因此，我们大多数人已经习惯于编写如下代码:

```
public class MyClass {
    private String data;
    public String getData() { return this.data; }
    public void setData(String data) { this.data = data; }
}
```

在上面的例子中，我们看到了一个具有简单属性的简单类:一个名为 *data* 的`String`。当然，*数据*既可以包含一个值，也可以是 *null* 。

我们如何处理`Collection`类型的属性？让我们构造一个简单的类来封装一个`List`:

```
public class MyClass { private List<String> myStrings;}
```

我们有一个名为`myStrings`的字段——一列`String`,它封装在`MyClass`中。现在，我们需要提供访问器方法:

```
public class MyClass { private List<String> myStrings; public void setMyStrings(List<String> s) {
        this.myStrings = s;
    } public List<String> getMyStrings() {
        return this.myStrings;
    }}
```

这里我们有一个适当封装的——虽然冗长——类。所以我们做得很好，对吗？保持这种想法。

# 选修课

考虑 Java 8 中引入的`Optional`类。到目前为止，您可能已经听说过这样一句口头禅:永远不要从返回`Optional`的方法中返回 *null* 。为什么？考虑下面这个虚构的例子:

```
public class Foo { private String bar; public Optional<String> getBar() {
        // don't actually ever do this
        return (bar == null) ? null : Optional.of(bar);
    }}
```

现在客户可以像这样使用这个方法，并冒着抛出`NullPointerException`的风险:

```
foo.getBar().ifPresent(log::info);
```

或者，他们可以执行空检查:

```
if (foo.getBar() != null) {
    foo.getBar().ifPresent(log::info);
}
```

当然，我们永远不会写这样的方法。这样做违背了`Optional` s 的目的。事实上，它违背了`Optional` s 的目的，即任何返回`Optional`的 API 都不会*返回空值。*

![](img/409fbb03c70ee8123ef54d432538d180.png)

即使所有的袜子都拿掉了，抽屉还是存在的，对吧？

回到`Collection` s. `Collection` s 和`Optional` s 其实是类似的野兽。它们都是用来保存其他值的“容器”对象。一个`Optional`包含*无*或*有一个*，而一个`Collection`包含*无*或*有*。(那些熟悉函数式编程的人可能会认为`Collection` s 和`Optional` s 是[单子](https://samgrayson.me/2019-08-06-monads-as-a-programming-pattern/)，尽管 Java 8 决定使用`Stream` s 意味着这在技术上是不正确的。)

因此，应该没有理由返回空的*`Collection`s。简单地返回一个空的(零大小)`Collection`来指示缺少任何元素。*

*正是因为这个原因，确保返回`Collection`类型(包括数组)的方法永远不返回 *null* 值变得越来越常见，这与返回`Optional`类型的方法相同。也许您或您的组织已经在编写新代码时采用了这条规则。如果没有，你应该。毕竟，你(或你的客户)愿意这样做吗？：*

```
*boolean isUnique = personDao.getPersonsByName(name).size() == 1;*
```

*或者您的代码中有类似这样的东西吗？：*

```
*List<Person> persons = personDao.getPersonsByName(name);
boolean isUnique = (persons == null) ? false : persons.size() == 1;*
```

# *掌控我们的收藏*

*但是还有更多的东西。让我们回到我们的`MyClass`课。事实上，`MyClass`的实例可以很容易地从`getMyStrings()`方法返回`null`。事实上，一个新的实例就可以做到这一点。因此，为了遵守我们新的*永不返回空集合*准则，我们需要解决这个问题:*

```
*public class MyClass { private List<String> myStrings = new ArrayList<>(); public void setMyStrings(List<String> s) {
        this.myStrings = s;
    } public List<String> getMyStrings() {
        return this.myStrings;
    }}*
```

*问题解决了？不完全是。任何客户都可以调用`aMyClass.setMyStrings(null)`，在这种情况下，我们又回到了起点。所以让我们扩展一下`setMyStrings()`方法:*

```
*public void setMyStrings(List<String> s) {
    if (s == null) {
        this.myStrings.clear();
    } else {
        this.myStrings = s;
    }
}*
```

*现在，即使将`null`传递给 setter，`myStrings`也会保留一个有效的引用。我们都说完了吗？*

*算是吧。我们可以停在这里。但是真的，我们应该做更多的事情。*

*考虑到我们仍然在用调用者传递给我们的`List`替换我们的私有`ArrayList`。这有两个问题。首先，我们不再控制`myStrings`使用的具体的`List`实现。理论上这应该不成问题吧？但是，请考虑以下情况:*

```
*myClass.setMyStrings(Collections.unmodifiableList("Heh, gotcha!"));*
```

*如果我们更新了`MyClass`，使得它试图修改`myStrings`的内容，运行时就会发生不好的事情。*

*第二个问题是调用者保留了对`MyClass`的底层`List`的引用。所以现在，调用者可以直接操作我们的`List`。*

*我们应该做的是将传递给我们的元素存储在初始化了`myStrings`的`ArrayList`中。此外，我们应该对外部调用者隐藏类的内部。事实上，我们的类的调用者不应该关心是否有底层的`List`、或者`Set`、或者数组、或者一些运行时动态代码生成巫毒术存储我们传递给它的`String`。他们应该关心的是设置和检索一组`String` s。所以让我们更新`setMyStrings()`方法:*

```
*public void setMyStrings(Collection<String> s) {
    this.myStrings.clear(); 
    if (s != null) { 
        this.myStrings.addAll(s); 
    } 
}*
```

*这具有确保`myStrings`以包含在输入参数中的相同元素结束(或者如果传递了`null`则为空)的效果，同时确保调用者没有对`myStrings`的引用。*

*既然`myStrings`引用不能改变，我们就让它保持不变吧:*

```
*public class MyClass {
    private final List<String> myStrings = new ArrayList<>();
    ...
}*
```

*当我们这么做的时候，我们不应该通过 getter 返回底层的`List`。这也会让打电话的人直接提到`myStrings`。为了补救这一点，回想一下*有效 Java* 灌输到我们头脑中的“防御性复制”咒语(或者应该有):*

```
*public List<String> getMyStrings() {
    // depending on what, exactly, we want to return
    return new ArrayList<>(this.myStrings);  
}*
```

*至此，我们有了一个封装良好的类，每当调用它的 getter 时，它都不需要进行空检查。然而，我们已经从客户那里拿走了一些控制权。因为他们不再能直接访问我们的底层`List`，他们不再能添加或删除单个的`String`。*

*没问题—我们可以像这样简单地添加方法:*

```
*public void addString(String s) {
    this.myStrings.add(s);
}*
```

*还有这个:*

```
*public void removeString(String s) { 
    this.myStrings.remove(s); 
}*
```

*我们的调用者可能需要一次向一个`MyClass`实例添加多个`String`吗？这也很好:*

```
*public void addStrings(Collection<String> c) {
    if (c != null) {
        this.myStrings.addAll(c);
    }
}*
```

*以此类推:*

```
*public void clearStrings() {
    this.myStrings.clear();
}public void replaceStrings(Collection<String> c) {
    clearStrings();
    addStrings(c); 
}*
```

# *收集我们的想法*

*那么，下面是我们的(公认是人为的)类最终可能的样子:*

```
*public class MyClass { private final List<String> myStrings = new ArrayList<>(); public void setMyStrings(Collection<String> s) {
        this.myStrings.clear(); 
        if (s != null) { 
            this.myStrings.addAll(s); 
        } 
    } public List<String> getMyStrings() {
        return new ArrayList<>(this.myStrings);
    } public void addString(String s) { 
        this.myStrings.add(s); 
    } public void removeString(String s) { 
        this.myStrings.remove(s); 
    } // And maybe a few more helpful methods...}*
```

*这样，我们实现了一个类:*

*   *仍然是一个符合 JavaBean 规范的 POJO*
*   *完全封装其私有成员*

*最重要的是确保它返回`Collection`的方法总是这样做——返回`Collection` ——而从不返回`null`。*

*觉得这个故事有用？想多读点？只需[在此订阅](https://dt-23597.medium.com/subscribe)即可将我的最新故事直接发送到您的收件箱。*

*今天[成为媒体会员](https://dt-23597.medium.com/membership)，你也可以支持我和我的写作，并获得无限数量的故事。*