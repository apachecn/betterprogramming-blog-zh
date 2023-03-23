# 轻松理解 JavaScript 闭包

> 原文：<https://betterprogramming.pub/understand-javascript-closures-with-ease-aa1c808a929c>

## 闭包允许 JavaScript 程序员编写更好的代码

![](img/090c106797b59211e68b22eb18d5b4ac.png)

闭包允许 JavaScript 程序员编写更好的代码。有创意，有表现力，简洁。我们经常在 JavaScript 中使用闭包，不管您的 JavaScript 经验如何，您无疑会一次又一次地遇到它们。

当然，闭包可能看起来很复杂，超出了您的范围，但是在您阅读了本文之后，闭包会变得更容易理解，从而对您的日常 JavaScript 编程任务更有吸引力。

这是一篇关于 JavaScript 中闭包细节的相对简短(也很有趣)的文章。在进一步阅读之前，您应该熟悉 JavaScript 的变量范围，因为要理解闭包，您必须理解 [JavaScript 的变量范围。](https://www.geeksforgeeks.org/understanding-variable-scopes-in-javascript/)

# 什么是终结？

闭包是一个内部函数，它可以访问外部(封闭)函数的变量——作用域链。

闭包有三个作用域链:

*   它可以访问自己的作用域(花括号中定义的变量)。
*   它可以访问外部函数的变量。
*   它可以访问全局变量。

内部函数不仅可以访问外部函数的变量，还可以访问外部函数的参数。注意，内部函数不能调用外部函数的*参数*给 object，尽管它可以直接调用外部函数的参数。

通过在另一个函数中添加一个函数来创建闭包。闭包在 [Node.js](https://nodejs.org/) 中被大量使用；它们是 Node.js 异步、非阻塞架构中的主力。

闭包也经常用在 jQuery 和你阅读的每一段 JavaScript 代码中。

JavaScript 中闭包的一个基本例子:

```
**function showName (firstName, lastName) {**
**var nameIntro = "Your name is ";**
*// this inner function has access to the outer function's     //variables, including the parameter*
**function makeFullName () {     
return nameIntro + firstName + " " + lastName;   
}
return makeFullName ();
}****showName ("Michael", "Jackson");** // Your name is Michael Jackson
```

# 关闭规则和副作用

## ***闭包可以访问外部函数的变量，即使在外部函数返回*** 之后

闭包最重要也是最棘手的特性之一是内部函数仍然可以访问外部函数的变量，即使外部函数已经返回。

是的，你没看错。当 JavaScript 中的函数执行时，它们使用与创建时相同的作用域链。

这意味着即使外部函数已经返回，内部函数仍然可以访问外部函数的变量。因此，您可以稍后在程序中调用内部函数。

该示例演示了:

```
function celebrityName (firstName) {
    var nameIntro = "This celebrity is ";
// this inner function has access to the outer function's               //variables, including the parameter
   function lastName (theLastName) {
        return nameIntro + firstName + " " + theLastName;
    }
    return lastName;
}

var mjName = celebrityName ("Michael"); // At this juncture, the  //celebrityName outer function has returned.

// The closure (lastName) is called hereafter the outer function //has returned above
// Yet, the closure still has access to the outer function's //variables and parameter
mjName ("Jackson"); // This celebrity is Michael Jackson
```

## **闭包存储对外部函数变量的引用**

它们不存储实际值。

当外部函数的变量值在闭包被调用之前改变时，闭包变得更加有趣。这种强大的功能可以通过创造性的方式加以利用，比如这个私人变量的例子，首先由道格拉斯·克洛克福特证明:

```
**function celebrityID () {
var celebrityID = 999;**
//We are returning an object with some inner functions
//All the inner functions have access to the outer function's //variables
   ** return {
        getID: function ()  {**
//This inner function will return the UPDATED celebrityID variable
//It will return the current value of celebrityID, even after the //changeTheID function changes it
          **return celebrityID;
        },
        setID: function (theNewID)  {**
//This inner function willchange outer function's variable anytime
           **celebrityID = theNewID;
        }
    }
}** 
**var mjID = celebrityID ();** // At this juncture, the celebrityID //outer function has returned.
**mjID.getID();** // 999
**mjID.setID(567);** // Changes the outer function's variable
**mjID.getID();** // 567: It returns the updated celebrityId variable
```

## **闭包出错**

因为闭包可以访问外部函数变量的更新值，所以当外部函数的变量随着`for-loop`改变时，它们也会导致错误。

因此:

```
// This example is explained in detail below (just after this code box).
**function celebrityIDCreator (theCelebrities) {
    var i;
    var uniqueID = 100;
    for (i = 0; i < theCelebrities.length; i++) {
      theCelebrities[i]["id"] = function ()  {
        return uniqueID + i;
      }
    }
    return theCelebrities;
}

var actionCelebs = [{name:"Stallone", id:0}, {name:"Cruise", id:0}, {name:"Willis", id:0}];

var createIdForActionCelebs = celebrityIDCreator (actionCelebs);

var stalloneID = createIdForActionCelebs[0]console.log(stalloneID.id());** // 103
```

在前面的例子中，当匿名函数被调用时，`i`的值是 3(数组的长度，然后它递增)。

数字 3 被加到了`uniqueID`上，为所有的`celebritiesID`创造了`103`。因此，返回数组中的每个位置都得到 id = `103`，而不是预期的 100、101、102。

发生这种情况的原因是，正如我们在前面的例子中所讨论的，闭包(本例中的匿名函数)可以通过引用访问外部函数的变量，而不是通过值。

因此，正如前面的例子显示我们可以用闭包访问更新的变量一样，这个例子同样在变量`i`改变时访问它，因为外部函数运行整个 for 循环并返回最后一个值`i`，即`103`。

要修复闭包中的这种副作用(bug ),可以使用立即调用的函数表达式(IIFE ),如下所示:

```
**function celebrityIDCreator (theCelebrities) {
    var i;
    var uniqueID = 100;
    for (i = 0; i < theCelebrities.length; i++) {
        theCelebrities[i]["id"] = function (j)  {** 
//the j parametric variable is the i passed in on invocation of this //IIFE
 **return function () {
                return uniqueID + j;** // each iteration of the for //loop passes the current value of i into this IIFE and it saves the //correct value to the array
           ** } ()** // BY adding () at the end of this function, we are //executing it immediately and returning just the value of //uniqueID+ j, instead of returning a function.
       ** } (i);** // immediately invoke the function passing the i //variable as a parameter
 **}

    return theCelebrities;
}** 
**var actionCelebs = [{name:"Stallone", id:0}, {name:"Cruise", id:0}, {name:"Willis", id:0}];

var createIdForActionCelebs = celebrityIDCreator (actionCelebs);**

**var stalloneID = createIdForActionCelebs [0];
console.log(stalloneID.id);** // 100

**var cruiseID = createIdForActionCelebs [1];
console.log(cruiseID.id);** // 101
```