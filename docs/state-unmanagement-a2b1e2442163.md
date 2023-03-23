# 国家失控

> 原文：<https://betterprogramming.pub/state-unmanagement-a2b1e2442163>

## 如何利用 DOM 避免管理状态

![](img/0945a79eafecca865218784c581977f7.png)

凯利·西克玛在 [Unsplash](https://unsplash.com/photos/377gw1wN0Ic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当 React 问世时，它是第一个明确向大众推销“作为国家功能的视图”的框架。无论如何，这个想法并不新颖，但是 React 无疑使它普及了。虽然可以使用较低层次的可观察对象，比如在 [KnockoutJS](https://knockoutjs.com/) 、[、](https://en.wikipedia.org/wiki/Virtual_DOM)中使用的那些，但似乎更适合这个想法，随后，我们今天知道的大多数框架都是在某种形式的 VDOM 之上实现的。不过，也有非 VDOM 图书馆，如 Svelte 和 Solid，体现了相同的基本思想。

如今，V(S)(视之为国家职能)在我们的行业中根深蒂固，对大多数人来说，似乎没有其他办法。当然，至少有一个。

在本文中，我们将探索另一种方法，我还将展示一些实现它的代码。虽然这种方法无论如何都不是灵丹妙药，也没有明确的宗教信仰，但它解决了开发 UI 时可能遇到的大多数问题，而且代码更少，更重要的是复杂性更低。

为了给你的旅程做好准备，我会马上做好三件事。

第一，我是**不是**在说 React 中[不受控制的组件。我说的是完全不同的东西，甚至是用 React(或任何 V(S)库)都做不到的东西。如果在读完本文后，您仍然认为这是关于不受控制的组件，请仔细考虑下一段。](https://medium.com/@hayavuk/react-forms-d49ec73cc84a)

其次，如果你不想亲自尝试这些东西，也不要指望完全理解这篇文章。本文描述了这些概念并提供了一些背景知识，但这还不够。为了完全理解一个新概念，你必须实践它。这篇文章中的信息实际上是实践，什么食谱的蛋糕是它的味道。我们这样做是为了吃的体验，而不是关于烹饪的知识！

最后，祝你新年快乐，我亲爱的读者！愿 2023 年带给你许多个月的快乐和发现！

# 当状态定义视图时

在 V(S)中，是状态定义了视图。这个想法是，我们有一个普通 JavaScript 对象形式的视图的抽象表示，然后我们有一个将这个状态同步到 DOM 的机制。

即使在切换到 vanilla 之后，有一段时间，我仍然在变量中管理应用程序状态，并手动将其同步到 DOM，这主要是出于惯性。我会在变量中保存一些值，然后我会先修改变量，然后在一个单独的过程中将它们与 DOM 同步。这样做的话，将值与 DOM 同步的代码不需要关心*值是如何*修改的。这看起来是一个巧妙的技巧，从精神上来说，仍然是 V(S)。关注点的分离等等。

那个数字变了，所以我需要改变这个节点的`textContent`，但是我不在乎是什么改变了数字。这个列表发生了变化，所以我需要更新那些表格行和这里的摘要文本，但是我不关心列表是如何被修改的。诸如此类。

另一方面，这帮助我敏锐地意识到所涉及的额外成本。*知道*这件事是一回事。虽然在简单的视图中手工实现了整个机器(双关语，无意)，但我开始觉得这是一种非常浪费的方法。

这让我开始质疑这种间接性的整个概念。为什么这是必要的？更重要的是，我们真的需要它吗？如果您正在使用框架，您可能甚至不知道，或者没有选择，因为这些细节总是对您隐藏的(这是抽象的一点)，但是它仍然存在。

# 状态二元性

DOM 是有状态的。这是显而易见的，但是当我们对 V(S)投入如此之深，尤其是使用工具使这一事实变得不可见和/或显得无关紧要时，它可能会躲避我们很多年。从我开始接触 JavaScript 的时候我就知道 DOM 是有状态的，但是后来我又学会了如何忽略这一点，并且忽略了很多年。

如果您在输入中键入内容，输入会保留文本。如果我们激活一个选择列表，它会一直打开。如果我们选中一个复选框，它将保持选中状态。我们可以修改各种属性的值，这些值会保持不变，直到节点被删除。这是所有的状态。

当我们做 V(S)时，我们基本上是在处理两种状态，一种在应用程序中，一种在 DOM 中。为了得到视图是状态的严格函数的错觉，我们需要确保状态的变化总是反映在 DOM 中，这就需要对 DOM 进行微观管理。

如果 DOM 是有状态的，并且我们正在开发接口——这意味着 DOM 是这个节目的真正明星，状态/数据只是在那里支持它——为什么我们不使用 DOM 作为事实的单一来源并将应用程序状态存储在其中呢？

# 在 DOM 中存储状态

让我解释一下我所说的“在 DOM 中存储状态”是什么意思。上面我举了几个例子，但是这里有一些具体的代码。

```
// Element text
let $output = document.getElementById('output')
$output.textContent

// Element values and properties as state
let $input = document.querySelector('input[name=email]')
$input.value = 'test'
$input.disabled

// Attributes and data attributes
let $th = document.querySelector('th[aria-sort]')
$th.getAttribute('aria-sort')
$th.dataset.active = true
```

我们通过访问各种属性从 DOM 中读取状态。我们通过设置属性来设置状态。是啊，我知道。完全没有戏剧性。这才是重点。这项功能在浏览器中，是免费的，使用起来也并不困难。

与普遍的看法相反，这一点也不慢。根据手头的问题，这可能不总是最快的方法，但绝对不会慢。我将在最后讨论一些注意事项。

当从 DOM 中读取状态时，除了少数情况下的，一般不会有太多的开销[。当写入 DOM 节点属性时，可能会有开销，也可能没有，这取决于您所写的内容(例如，许多属性需要反映在属性中，因此这显然与在普通对象上分配属性不完全相同)。如果您使用非标准属性，这本质上与在任意随机对象上为任意属性赋值是一样的，因此没有额外的开销。记住，DOM 节点只是对象。他们不是什么神奇的野兽，如果你碰他们，他们会咬掉你的头！](https://gist.github.com/paulirish/5d52fb081b3570c81e3a)

我们还有一个仅在会话期间相关的临时状态。例如，用于优化的缓存值。我们也可以使用非标准属性将这些值存储在 DOM 节点上。例如:

```
let 
  $input = document.querySelector('input[name=email]'),
  markTouched = () => $input.__touched = true

$input.addEventListener('change', markTouched, { once: true })
```

`__touched`属性是非标准的(我用双下划线作为前缀，以避免与未来的标准属性发生冲突)。因为它代表了某种短暂的状态，这种状态只在用户真正接触界面时才相关，所以以一种不能在服务器端序列化为 HTML 的方式存储它是很好的。

当然，这种类型的状态也可以使用数据属性来存储，如果状态会影响样式，这可能是一种更好的方法，因为数据属性像任何其他属性一样可以在 CSS 中选择。

# DOM 状态和 CSS

当使用 DOM 状态作为应用程序状态时，我们也更习惯于在 CSS 中使用各种属性作为选择器。例如，我们可以有这样的选择器:

```
input[value],
input:checked,
th[aria-sort=ascending]::before,
button:disabled,
...
```

这并不是直接使用 DOM state 所特有的，而是这种方法的一个更自然的伴侣。

在 React 中，也没有什么可以阻止任何人这样做。只是当开发人员可以访问变量中的状态时，他们倾向于设置类，可能还有一小部分其他属性，比如`disabled`。

# 比较

让我在托管和非托管状态之间做一个简单的直接比较。我将使用普通的 DOM API 来做这件事，没有框架。

让我们构建一个简单的有三个选择的生日选择器。HTML 将由三个选择列表组成，一个包含可用年份的范围，一个包含 12 个月，一个包含 31 天。由于并非所有可能的组合都是有效的日期，我们将根据选择修改可用的月份和日期。

在这两种情况下，HTML 都将使用如下所示的代码在服务器端或客户端呈现:

```
let 
  today = new Date(),
  validYears = (() => {
    let years = []
    for (let i = 1970; i < today.getFullYear(); years.push(i++)) {}
    return years
  })(),
  validMonths = (() => {
    let
      months = [],
      maxMonth = today.getMonth()
    for (let i = 0; i <= maxMonth; i++)
      months.push([
        i, 
        new Date(year, i, 1)
          .toLocaleDateString(navigator.language, { month: 'long' })
      ])
    return months
  })(),
  validDays = (() => {
    let
      days = [],
      lastDay = today.getDate()
    for (let i = 1; i <= lastDay; days.push(i++)) {}
    return days
  })(),
  html = `
    <select name="year" >
      ${validYears.map(n => `<option value="${n}">${n}</option>`)}
    </select>
    <select name="month">
      ${validMonths.map(([n, name]) => `<option value="${n}">${name}</option>`)}
    </select>
    <select name="day">
      ${validDays.map(n => `<option value="${n}">${n}</option>`)}
    </select>
  ` 
```

让我们从托管版本开始。这段代码有意全部是手动的，以便稍后清楚地显示与非托管版本的区别。

```
let
  today = new Date(),
  selectedYear = 1990,
  selectedMonth = 0, // 0-based, just like the Date object
  selectedDay = 1,

  $year = document.querySelector('select[name=year]'),
  $month = document.querySelector('select[name=month]'),
  $day = document.querySelector('select[name=day]'),

  adjustDay = () => {
    if (new Date(selectedYear, selectedMonth, selectedDay).getMonth() !== selectedMonth)
      $day.value = selectedDay = new Date(selectedYear, selectedMonth + 1, -1)
  },
  updateMonths = () => {
    let lastMonthToShow = selectedYear < today.getFullYear() ?
      11 : today.getMonth()
    for (let $ of $month.children) 
      $.hidden = $.value > lastMonthToShow
  },
  updateDates = () => {
    let lastDayToShow = new Date(Math.min(
      new Date(selectedYear, selectedMonth + 1, -1)
      today
    )).getDate()
    for (let $ of $day.children)
      $.hidden = $.value > lastDayToShow
  }

$year.onchange = () => {
  selectedYear = Number($year.value)
  adjustDay()
  updateMonths()
  updateDates()
}
$month.onchange = () => {
  selectedMonth = Number($month.value)
  adjustDay()
  updateDates()
}
$date.onchange = () => {
  selectedDay = Number($day.value)
}
```

现在同样的东西以非托管格式出现。

```
let 
  today = new Date(),

  $year = document.querySelector('select[name=year]'),
  $month = document.querySelector('select[name=month]'),
  $day = document.querySelector('select[name=day]'),

  adjustDay = () => {
    if (new Date($year.value, $month.value, $day.value).getMonth() !== $month.value)
      $day.value = new Date($year.value, Number($month.value) + 1, -1)
  },
  updateMonths = () => {
    let lastMonthToShow = $year.value < today.getFullYear() ?
      11 : today.getMonth()
    for (let $ of $month.children) 
      $.hidden = $.value > lastMonthToShow
  },
  updateDates = () => {
    let lastDayToShow = new Date(Math.min(
      new Date($year.value, Number($month.value) + 1, -1)
      today
    )).getDate()
    for (let $ of $day.children)
      $.hidden = $.value > lastDayToShow
  }

$year.onchange = () => {
  adjustDay()
  updateMonths()
  updateDates()
}
$month.onchange = () => {
  adjustDay()
  updateDates()
}
```

这两段代码非常相似，但肯定有一些不同之处。在第二个版本中:

*   我们不再有代表当前所选选项值的三个变量
*   我们直接使用选择列表的值
*   我们只是有选择地对值进行类型转换，并且主要依靠运行时来强制转换(从技术上讲，这也是您在托管版本中可以做的事情，但是由于管理状态的全部目的是为了促进松散耦合，所以您不会这样做，因为您不知道何时以及如何使用这些值)
*   我们少了一个事件处理程序
*   我们不再需要将输入值赋给变量

在这个例子中，我们能够删除 40 行代码中的 8 行(不包括空格)，所以大约每五行中就有一行。我们真的不能就代码占用空间的潜在节省得出结论，因为这取决于编码风格、所解决的问题等等。不过，一般来说，代码量的差异随着应用程序的大小而增加。换句话说，回报不会减少——而是增加——后面会有一些警告。

# 比较反应

作为一个有趣的练习(或者不是)，让我们也做一个反应版本。现在我的反应有点迟钝了，所以我可能做这些事情的效率比他们应该做的要低一些，但是我保证我不会试图增加它的几率。

```
import React from 'react'

let
  today = new Date(),
  validYears = (() => {
    let years = []
    for (let i = 1970; i < today.getFullYear(); years.push(i++)) {}
  })(),
  getValidMonths = (year, month) => {
    let
      months = [],
      maxMonth = Math.min(month, today.getMonth())
    for (let i = 0; i <= maxMonth; i++)
      months.push([
        i, 
        new Date(year, i, 1)
          .toLocaleDateString(navigator.language, { month: 'long' })
      ])
    return months
  }
  getValidDays = (year, month, day) => {
    let
      days = [],
      lastDay = new Date(Math.min(
        new Date(year, month, day),
        today
      )).getDate()
    for (let i = 1; i <= lastDay; i++) 
      days.push(i)
    return days
  }
  BirthdaySelect = class extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        // Actual state we are interested in
        selectedYear: 1990,
        selectedMonth: 0,
        selectedDay: 1,

        // Abstract representation of the UI required for VDOM
        selectableMonths: [],
        selectableDays: [],
      }
    }

    handleYear = ev => {
      let year = Number(ev.target.value)
      this.setState(state => ({
        selectedYear: year,
        selectableMonths: this.getValidMonths(year, state.selectedMonth),
        selectableDays: this.getValidDays(
          year, 
          state.selectedMonth, 
          state.selectedDay,
        ),
      })
    }
    handleMonth = ev => {
      let month = Number(ev.target.value)
      this.setState(state => ({
        selectedMonth: month,
        selectableDays: this.getValidDays(
          year, 
          state.selectedMonth, 
          state.selectedDay,
        )
      })
    }
    handleDay = ev => {
      this.setState(() => ({ 
        selectedDay: Number(ev.target.value),
      })
    }

    render() {
      return (
        <>
          <select 
            name="year" 
            value={this.state.selectedYear} 
            onChange={this.handleYear}
            >
            {validYears.map(n => 
              <option value={n} key={n}>{n}</option>}
          </select>
          <select 
            name="month"
            value={this.state.selectedMonth}
            onChange={this.handleMonth}
            >
            {this.state.selectableMonths.map(([n, label]) =>
              <option value={n} key={n}>{label}</option>}
          </select>
          <select 
            name="day"
            value={this.state.selectedDay}
            onChange={this.handleDay}
            >
            {this.state.selectableDays.map(n =>
              <option value={n} key={n}>{n}</option>}
          </select>
        </>
      )
    } 
```

React 做的事情有点不同，首先它需要自己创建 DOM 节点，而不是让浏览器来处理。

为了呈现 DOM 节点，我们还需要计算与 UI 结构匹配的状态导数，这样我们就可以很好地将它们映射到节点上。`getValidMonths()`和`getValidDays()`函数复制了前两个例子中相似的功能，所以我们不一定把它们算作特定于 React 的。一个显著的区别是，每当底层数据发生变化时，我们都必须调用这些函数。这是需要使用数据表示 UI 而不是直接使用 DOM 的结果。为了方便起见，我们还将选择的有效值存储在组件状态中，以避免为了迂腐而必须实现记忆化和类似的技巧。无论哪种方式，我们都无法避免额外的代码。从这个意义上说，这些确实代表了额外的开销。不是一个大的，当然，但仍然开销。

代码的其余部分或多或少类似于托管状态的普通示例——如果你仔细观察的话——类似于我们在那里看到的 cruft。

最重要的是，React 甚至没有为我们提供任何其他方式的选项，这意味着我们注定会遇到解决方案不是最佳的和/或比它本来可能的更复杂的情况。

# 管理或取消管理

我希望我听起来不像非托管状态是银弹。虽然我确实认为非托管状态在大多数情况下是一种很好的默认方法，但显然在某些情况下这是行不通的。

例如，如果您要处理成千上万的数据点，并希望将其绘制成图表或转换成其他形式供人们使用，那么您显然不能将所有这些数据点存储在一个 HTML 表中，并将其称为“非托管状态”。这将是非常低效和浪费的，因为任何包含数万个内容的东西都不会被最终用户使用(甚至是可用的)，即使这样做没有性能问题——当然有。

虽然使用 DOM 作为应用程序状态通常会导致代码更简单，但也不总是这样。除了我们已经提到的关于数据量和数据使用方式的内容，将状态作为 JavaScript 对象进行管理有时比在 DOM 中做任何事情都更好。有时，在将更改应用到 DOM 之前，有必要首先抽象地计算预期的结果。不是很经常，但是有一些这样的情况。

例如，虽然只使用 DOM 对表行进行排序和过滤可以大大简化代码，但是如果这两个操作总是结合在一起执行，那么首先在数据中执行*可以*减少 DOM 操作的数量(取决于您如何执行)。在最坏的情况下，您不会注意到差异，但在最好的情况下，托管方法最终可能会快一倍或更多，这取决于机器的性能和其他因素。在这些情况下，试图优化只使用 DOM 的代码的性能通常会导致代码比管理状态的代码更加*复杂，这违背了不管理状态的目的，不值得。*

那么，我们没有节点来存储状态这种明显的情况呢？比如说，在处理画布的时候。对于非托管状态，这也不是一个很好的用例。

最后，我还要提醒你，这不是一个非此即彼的问题。根据具体情况，这两种方法各有优势，并且可以在同一个应用程序中愉快地共存。我们也可以采用混合方法，在这种方法中，国家处于半管理状态——这可能是下一次的主题。只有当我们别无选择时(例如，当我们使用专门用于托管状态的库，如 React 和其他 V(S)库)或做出错误选择时，我们才会遇到问题。

# 示例代码

你可以在我的 GitHub 简介中找到一些国家失控的例子。

*   [普通的 TODO 应用程序](https://github.com/foxbunny/vanilla-todo)，它使用一个表单来表示应用程序的所有状态
*   [表格排序和过滤示例](https://github.com/foxbunny/vanilla-ui-patterns/tree/master/table-sorting-filtering)在我的[普通 UI 模式](https://github.com/foxbunny/vanilla-ui-patterns) repo 中
*   在[普通 UI 模式](https://github.com/foxbunny/vanilla-ui-patterns) repo 中的[基准测试部分](https://github.com/foxbunny/vanilla-ui-patterns/tree/master/benchmarks)包含了三个与状态管理相关的基准测试，展示了一个受管状态优于非受管状态的边缘案例(但是不要据此得出一概而论的结论！)