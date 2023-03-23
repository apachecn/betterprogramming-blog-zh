# 像编写 React 组件一样编写 React 挂钩

> 原文：<https://betterprogramming.pub/compose-react-hooks-like-composing-react-components-892c23530432>

## 为什么定制挂钩如此强大

![](img/b709bdff5b7562de6c15cdeab98343e4.png)

这篇短文展示了代码示例，说明了 [React Hooks](https://reactjs.org/docs/hooks-intro.html) 在组成方面如何类似于组件。让我们直接进入代码:

# 反应组分

让我们用一个简单的例子。这里有一个组件:

```
const **Person** = ({ person }) => (
  <div>
    <div className="personFirstName">
      <span>First Name:<span>
      <span>{person.firstName}</span>
    </div>
    <div className="personLastName">
      <span>Last Name:<span>
      <span>{person.lastName}</span>
    </div>
  </div>
);
```

这个组件有点大，所以让我们把它分成几个功能，然后组合起来:

```
const **FirstName** = ({ name }) => (
  <div className="personFirstName">
    <span>First Name:<span>
    <span>{name}</span>
  </div>
);const **LastName** = ({ name }) => (
  <div className="personLastName">
    <span>Last Name:<span>
    <span>{name}</span>
  </div>
);const **Person** = ({ person }) => (
  <div>
    <**FirstName** name={person.**firstName**} />
    <**LastName** name={person.**lastName**} />
  </div>
);
```

现在，它看起来很有条理。我们为什么要这么做？因为，现在我们可以在其他组件中使用`FirstName`组件了。简而言之，可重用性。

# 反应钩

React Hooks 呢？假设我们有`usePerson`钩子，它返回了`Person`对象。它是如何实现的并不重要，但是举个例子，我们可以假设它和`useContext`是一个简单的具体案例。

```
const **Person** = () => {
  const person = **usePerson**();
  const firstName = person.firstName;
  const lastName = person.lastName;
  return <div>{firstName}{' '}{lastName}</div>
};
```

这过于简化了，但是让我们把它分解成几个函数，然后组合起来。

```
const **useFirstName** = () => {
  const person = **usePerson**();
  return person.firstName;
};const **useLastName** = () => {
  const person = **usePerson**();
  return person.lastName;
};const **Person** = () => {
  const firstName = **useFirstName**();
  const lastName = **useLastName**();
  return <div>{firstName}{' '}{lastName}</div>;
};
```

我希望你明白我的意思。现在，你可以在其他组件中重用`useFirstName`钩子。

# 结论

构图很重要。一个组件可以用其他组件构建，一个钩子也可以用其他钩子构建。

# 附录

我们在组件方面有`children`。钩子中的等价物是什么？我不是 100%确定这一点，但它可能是钩子工厂。或者说，这只是它的一个变种。这是上面例子的代码。

```
const **createPersonHooks** = (**usePerson**) => {
  const **useFirstName** = () => {
    const person = **usePerson**();
    return person.firstName;
  };
  const **useLastName** = () => {
    const person = **usePerson**();
    return person.lastName;
  };
  return { **useFirstName**, **useLastName** };
};
```

再说一次，我不是很确定，只是一个想法。Constate 似乎也采取了类似的方法。