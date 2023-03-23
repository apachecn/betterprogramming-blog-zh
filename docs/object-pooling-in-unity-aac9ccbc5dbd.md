# Unity 中的对象池

> 原文：<https://betterprogramming.pub/object-pooling-in-unity-aac9ccbc5dbd>

## 使用新的 Unity Pooling API 优化性能

![](img/44c293da620368964064a5d1ba77c97b.png)

在 Unity 中，我们知道我们可以通过调用`Instantiate()`从预置创建一个游戏对象的新实例，并在稍后通过调用【惊喜】`Destroy()` *销毁它。很好，没什么问题。*这是方法*。*

但是，如果我们需要分配大量的对象并在短时间内销毁它们，这将对性能产生影响。于是 [*对象池*](https://gameprogrammingpatterns.com/object-pool.html) 就来了，化险为夷。

> 对象池是一种创造性的模式，它允许我们预先实例化一组对象并重用它们，而不是按需分配和销毁它们

我们将使用新的 Unity Pooling API，特别是 [*ObjectPool*](https://docs.unity3d.com/ScriptReference/Pool.ObjectPool_1.html) 类，它使用一个堆栈来保存对象实例的集合。因为它是一个泛型类，所以我们的目标也是创建一个可以重用的类来管理它。

问题是我们要使用的类型需要一个对池的引用，这样我们就可以通过调用 ObjectPool 的`Release()`方法来释放这个对象。

我们不希望每个类都重新定义同一个方法。取而代之的想法是创建一个基类，这个基类从`MonoBehaviour`派生，包含一个对池的引用，并且有一个我们将要用来释放对象的方法的基本实现。

由于`ObjectPool`是`[IObjectPool](https://docs.unity3d.com/ScriptReference/Pool.IObjectPool_1.html)` 接口的一个实现，我们将使用这个类型来保存引用。

> 编程到接口，而不是实现

接下来，我们将创建一个类来管理池。

我们将使用`GameObject`类型，因为它是所有实体的基类，但是我们的类将保存一个对从我们之前定义的 Poolable 类继承的预置的引用。

为了创建一个新的`ObjectPool`实例，我们需要定义一些回调来传递给它的[构造函数](https://docs.unity3d.com/ScriptReference/Pool.ObjectPool_1-ctor.html):

*   `CreatePooledItem`，用于在池为空时创建一个新实例
*   `OnTakeFromPool`，从池中取出实例时调用
*   `OnReturnedToPool`，当实例返回到池中时调用
*   `OnDestroyPoolObject`，当元素由于池达到最大值而无法返回池时调用

我们还需要为池定义默认容量和最大大小。布尔值用于启用集合检查。

这里没什么需要注意的。当对象被创建时，我们通过获取其`Poolable`组件将引用分配给池。当对象从池中取出或返回池中时，在`[OnEnable()](https://docs.unity3d.com/ScriptReference/MonoBehaviour.OnEnable.html)`或`[OnDisable()](https://docs.unity3d.com/ScriptReference/MonoBehaviour.OnDisable.html)`内的对象生命周期中，我们可能会执行一些清理和状态重置，或者将任务委托给负责生成对象的类。

现在，让我们创建一个从`Poolable`派生的类，并定义一个弹丸。

让我们创建一个预置来附加这个脚本，以及一个触发碰撞器。

在`Update`中，我们通过调用从 Poolable 类继承的函数来释放相机外的对象(Boundaries 只是我创建的一个保存这一点的枚举)。当投射物击中另一个标记为“敌人”的`GameObject`时，情况也是如此。

现在我们要实现一个简单的 spawner 来发射炮弹。首先，我们需要一个对我们的池的引用，然后我们定义一个空的`GameObject`作为父转换来保存我们将触发的所有实例，保持层次结构的整洁。我们在 Spawn 函数中设置初始位置和旋转。

我们现在剩下的就是获取一个对`SimpleSpawner`组件的引用，并在每次我们想用我们的播放器开火时调用`Spawn()`函数。

*随意玩*