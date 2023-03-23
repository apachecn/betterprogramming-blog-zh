# JavaScript 中高阶函数的威力

> 原文：<https://betterprogramming.pub/the-power-of-higher-order-functions-in-javascript-91fc8edc3a7a>

## 带有示例和使用案例

![](img/723f6f7cad679bb6c63633596f51c621.png)

*克里斯托弗·高尔在 Unsplash 上拍摄的照片*

几乎所有现存的 JavaScript 应用程序都使用 JavaScript 函数。由于函数，这种语言能够做许多强大的事情，例如在医疗保健中建立人工智能功能。

我将在这篇文章中谈论的一个强大功能是*高阶函数*。这是一个要么接受另一个函数作为参数，要么返回一个函数作为返回值的函数。

也就是说，如果你一直在玩 JavaScript，你可能已经听说过这个术语。高阶函数在 JavaScript 中被广泛使用，它们存在于常用的函数中，如`.map`、`.filter`、`.reduce`和`.forEach`。

如果您是 JavaScript 新手，您可能会对这些方法中的*和*高阶函数感到困惑。

当您将函数回调声明为这些数组方法的参数时，您会看到这些:

```
const arr = [1, 2, 3, 4, 5, 'six', 'seven', 'eight', 'nine', 'ten']

// Duplicate the array
arr.map(function(value) {
  return value
})

// Return only the number types
arr.filter(function(value) {
  return typeof value === 'number'
})

// Log each value to the console
arr.forEach(function(value) {
  console.log(value)
})

// Add the numbers together, avoiding the string types
arr.reduce(function(acc, value) {
  if (typeof value === 'number') {
    acc += value
  }
  return acc
}, 0)
```

但是高阶函数不是你传递给像`.map`这样的方法的函数。像`.map`这样的方法是高阶函数。

当我们提到高阶函数可以是以另一个函数作为参数的函数时，这正是当你传入一个函数时它所做的。

下面是一个功能与`.map`方法完全一样的实现:

```
function map(callback) {
  const result = []

  for (let index = 0; index < this.length; index++) {
    const currentItem = this[index]
    const returnValue = callback(currentItem, index, this)
    result.push(returnValue)
  }

  return result
}
```

查看代码片段，`callback`参数与我们作为参数传递给我之前展示的`.map`方法的函数完全相同:

```
*// Duplicate the array*
arr.map(function(value) {
  return value
})
```

更准确地说，让我将这段代码重命名为与我们的`map`函数实现相同的名称，这样您可以更清楚地看到它:

```
const callback = function(value) {
  return value
}

// Duplicate the array
arr.map(callback)

// is the same callback used in our .map implementation:
function map(callback) {
  const result = []
  for (let index = 0; index < this.length; index++) {
    const currentItem = this[index]
    const returnValue = callback(currentItem, index, this)
    result.push(returnValue)
  }
  return result
}
```

起初，这似乎是用 JavaScript 编写代码的无用方法。为什么传入一个函数并麻烦返回另一个函数，当你可以避免这一切并在一个函数中一次完成所有事情的时候？

高阶函数给表带来的最大好处是可重用性和简单性。但是他们也从帮助编写漂亮的代码中受益。是的，JavaScript 中有丑陋的代码和美丽的代码这样的东西。

考虑到可重用性，高阶函数引入了一些非常强大的代码组合。

# 代码组成和强大的例子

现在我们知道了高阶函数在代码中的样子，你可能想知道一些用例是什么，它们从哪里开始发光。

假设我们有一个青蛙列表:

```
const frogsList = [
  // Yes, these frogs are intelligent. They know how to use email
  {
    name: 'bobTheFrog',
    email: 'froggy@gmail.com',
    age: 2,
    gender: 'Male',
    widthOfTongue: 3,
  },
  {
    name: 'hippoTheFrog',
    email: 'hippo@gmail.com',
    age: 10,
    gender: 'Male',
    widthOfTongue: 11,
  },
  {
    name: 'sally',
    email: 'sallyLipstick@gmail.com',
    age: 5,
    gender: 'Female',
    widthOfTongue: 4,
  },
  {
    name: 'george',
    email: 'georgeRoseBowl@gmail.com',
    age: 11,
    gender: 'Male',
    widthOfTongue: 3,
  },
  {
    name: 'lisa',
    email: 'lisaLovesGeorgeForever@gmail.com',
    age: 19,
    gender: 'Female',
    widthOfTongue: 15,
  },
  {
    name: 'kentucky',
    email: 'frogInKentucky@yahoo.com',
    age: 18,
    gender: 'Male',
    widthOfTongue: 13,
  },
]
```

为了在没有高阶函数的情况下将青蛙筛选到特定的性别类型，我们必须做这样的事情:

```
function filterGender(gender, frogs) {
  return frogs.filter(function(frog) {
    return frog.gender ==== gender
  })
}

// filterGender in use
const maleFrogs = filterGender('Male', frogsList)
```

这很好。但是，如果在一个应用程序中多次使用，可能会很麻烦。如果我们有一个关于青蛙的巨大应用程序，`filterGender`可能会被多次使用。

# 更进一步

如果您要获取不同的青蛙列表，您必须再次调用`filterGender`,并重新声明您的性别作为过滤新列表的第一个参数:

```
function getFrogs() {
  // some logic and returns a new list of frogs
}

const newFrogs = getFrogs()
const moreMaleFrogs = filterGender('Male', newFrogs) // Shucks, I have to write 'Male' again?
```

如果你从未听说过[干](https://thevaluable.dev/dry-principle-explained/)原则，我强烈推荐你去了解一下。由于第一个参数，我们的代码片段违反了这条规则。我们可以做得更好。

为了解决这个问题，我们可以使用高阶函数的概念。

```
function filterGender(gender) {
  return function(frogs) {
    return frogs.filter(function(frog) {
      return frog.gender === gender
    })
  }
}
```

就像这样，我们可以把这个性别过滤器赋给一个变量，这样我们在过滤青蛙的时候就再也不用声明相同的性别了！

```
const filterFemaleFrogs = filterGender('Female')
const femaleFrogs = filterFemaleFrogs(frogsList)
```

但是等等，这还不是全部。通过组合它们，我们获得了一个额外的好处。我们不仅受益于再也不必为雌性青蛙重新编写过滤器，而且我们现在还能够重用返回的函数来从不同的青蛙列表中过滤相同性别的青蛙！

现在，我们可以从多个青蛙列表中筛选出雌性，而无需编写太多代码:

```
const frogsList = [
  // Yes, these frogs are intelligent. They know how to use email
  {
    name: 'bobTheFrog',
    email: 'froggy@gmail.com',
    age: 2,
    gender: 'Male',
    widthOfTongue: 3,
  },
  {
    name: 'hippoTheFrog',
    email: 'hippo@gmail.com',
    age: 10,
    gender: 'Male',
    widthOfTongue: 11,
  },
  {
    name: 'sally',
    email: 'sallyLipstick@gmail.com',
    age: 5,
    gender: 'Female',
    widthOfTongue: 4,
  },
  {
    name: 'george',
    email: 'georgeRoseBowl@gmail.com',
    age: 11,
    gender: 'Male',
    widthOfTongue: 3,
  },
  {
    name: 'lisa',
    email: 'lisaLovesGeorgeForever@gmail.com',
    age: 19,
    gender: 'Female',
    widthOfTongue: 15,
  },
  {
    name: 'kentucky',
    email: 'frogInKentucky@yahoo.com',
    age: 18,
    gender: 'Male',
    widthOfTongue: 13,
  },
]

const frogsList2 = [
  {
    name: 'abc',
    email: 'froggy@gmail.com',
    age: 2,
    gender: 'Male',
    widthOfTongue: 1,
  },
  {
    name: '123',
    email: 'hippo@gmail.com',
    age: 10,
    gender: 'Male',
    widthOfTongue: 4,
  },
  {
    name: 'joe',
    email: 'sallyLipstick@aol.com',
    age: 5,
    gender: 'Female',
    widthOfTongue: 6,
  },
  {
    name: 'jennifer',
    email: 'georgeRoseBowl@aol.com',
    age: 11,
    gender: 'Female',
    widthOfTongue: 10,
  },
]

const frogsList3 = [
  {
    name: 'professorHammick',
    email: 'froggy@gmail.com',
    age: 2,
    gender: 'Female',
    widthOfTongue: 1,
  },
  {
    name: 'macintosh',
    email: 'hippo@gmail.com',
    age: 10,
    gender: 'Female',
    widthOfTongue: 6,
  },
  {
    name: 'frogger',
    email: 'sallyLipstick@gmail.com',
    age: 5,
    gender: 'Female',
    widthOfTongue: 4,
  },
  {
    name: 'frogNation',
    email: 'georgeRoseBowl@gmail.com',
    age: 11,
    gender: 'Female',
    widthOfTongue: 4,
  },
]

function gatherFemaleFrogsEverywhere(...frogLists) {
  const allFemaleFrogs = []
  const filterFemaleFrogs = filterGender('Female')
  frogLists.forEach(function(list) {
    allFemaleFrogs.push(...filterFemaleFrogs(list))
  })
  return allFemaleFrogs
}

const females = gatherFemaleFrogsEverywhere(frogsList, frogsList2, frogsList3)
```

# 更进一步

如果您*仍然不相信*JavaScript 语言中的高阶函数有多强大，那么让我们继续这个例子，让*成为一个更通用的函数，*创建一个更高级别的可重用性:

```
function filterFrogs(filter) {
  return function(frogs) {
    return frogs.filter(filter)
  }
}
```

以前，我们有能力为青蛙的性别创建一个可重复使用的函数。然而，我们可以进一步抽象出`filter`函数的逻辑，这样现在我们就可以组合和重用*不同的过滤函数*！

```
const filterMaleFrogs = filterFrogs(function(frog) {
  return frog.gender === 'Male'
})

const filterAdultFrogs = filterFrogs(function(frog) {
  return frog.age >= 10
})

const filterFrogNamesThatStartWithHippo = filterFrogs(function(frog) {
  return frog.name.toLowerCase().startsWith('hippo')
})

const filterGmailEmails = filterFrogs(function(frog) {
  return /gmail.com/i.test(frog.email)
})
```

哇！

以前，我们有一个惊人的能力，可以重用性别过滤器函数，而不必再次声明相同的性别类型。现在我们有了额外的能力来创建和重用*我们希望如何过滤青蛙的函数—* 太棒了！

我们甚至可以同时使用它们:

```
function applyAllFilters(...filters) {
  return function(frogs) {
    let newFrogs = [...frogs]
    for (let index = 0; index < filters.length; index++) {
      const filter = filters[index]
      newFrogs = filter(newFrogs)
    }
    return newFrogs
  }
}

const applyFrogFilterers = applyAllFilters(
  filterMaleFrogs,
  filterAdultFrogs,
  filterFrogNamesThatStartWithHippo,
  filterGmailEmails,
)

const combinedFrogsList = [...frogsList, ...frogsList2, ...frogsList3]

const filteredFrogs = applyFrogFilterers(combinedFrogsList)

console.log(filteredFrogs)

/*
      result:
        {
          age: 10,
          email: "hippo@gmail.com",
          gender: "Male",
          name: "hippoTheFrog",
          widthOfTongue: 11
        }
*/
```

# 最后一次更进一步

我们的`applyAllFilters`函数做得很好。然而，对于庞大的青蛙列表，这可能会成为一项繁重的任务，因为它要多次运行`filter`才能得到最终结果。

我们可以再次使用高阶函数的概念来制作一个简单的、可重用的高阶函数，它能够通过同时应用过滤器来使一个*通过整个青蛙列表。*

更清楚地说，看一下 *for 循环*代码，试着看看幕后真正发生了什么:

```
function applyAllFilters(...filters) {
  return function(frogs) {
    let newFrogs = [...frogs]
    for (let index = 0; index < filters.length; index++) {
      const filter = filters[index]
      newFrogs = filter(newFrogs)
    }
    return newFrogs
  }
}
```

我想让你看看这条线:

```
newFrogs = filter(newFrogs)
```

该行代码与该函数中的`return frogs.filter(filter)`代码相同:

```
function filterFrogs(filter) {
  return function(frogs) {
    return frogs.filter(filter)
  }
}
```

这是一个问题，因为过滤方法*创建了一个新的数组*。当我们写下这些时:

```
const applyFrogFilterers = applyAllFilters(
  filterMaleFrogs,
  filterAdultFrogs,
  filterFrogNamesThatStartWithHippo,
  filterGmailEmails,
)
```

我们调用 filter 方法四次。换句话说，我们让 JavaScript 在内存中创建四个不同的数组，只是为了得到最终结果。

那么，我们如何让 JavaScript 只创建一个*T4 数组，最终得到相同的结果呢？*

你猜对了。使用高阶函数！

```
// NOTE: The filter functions are now individual functions (not wrapped with filterFrogs)const filterMaleFrogs = function(frog) {
  return frog.gender === 'Male'
}const filterAdultFrogs = function(frog) {
  return frog.age >= 10
}const filterFrogNamesThatStartWithHippo = function(frog) {
  return frog.name.toLowerCase().startsWith('hippo')
}const filterGmailEmails = function(frog) {
  return /gmail.com/i.test(frog.email)
}function combineFilters(...fns) {
  return function(val) {
    for (let i = 0; i < fns.length; i++) {
      const filter = fns[i]
      const passes = filter(val)
      if (passes) {
        continue
      } else {
        return false
      }
    }
    return true
  }
}function composeFrogFilterers(...fns) {
  return function(frogs) {
    return frogs.filter(combineFilters(...fns))
  }
}const applyFrogFilterers = composeFrogFilterers(
  filterMaleFrogs,
  filterAdultFrogs,
  filterFrogNamesThatStartWithHippo,
  filterGmailEmails,
)const combinedFrogsList = [...frogsList, ...frogsList2, ...frogsList3]const allFilteredFrogs = applyFrogFilterers(combinedFrogsList)console.log(allFilteredFrogs)/*
      result:
        {
          age: 10,
          email: "[hippo@gmail.com](mailto:hippo@gmail.com)",
          gender: "Male",
          name: "hippoTheFrog",
          widthOfTongue: 11
        }
*/
```

# 结论

我希望您相信高阶函数是多么强大，并且通过阅读本文，您已经深入了解了这个概念的用例。

以后多多关照！