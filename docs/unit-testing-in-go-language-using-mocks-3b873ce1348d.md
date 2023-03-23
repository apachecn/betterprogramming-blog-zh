# Go 语言中的单元测试

> 原文：<https://betterprogramming.pub/unit-testing-in-go-language-using-mocks-3b873ce1348d>

## 以及如何使用模拟

![](img/2f28a2faa2e2d41ca976cb5181274fa8.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

单元测试在软件开发中起着至关重要的作用。对程序中的每个代码块进行单元测试总是更好。在这里，我们将讨论以下主题:

1.  单元测试的几个优点
2.  编写单元测试的示例程序
3.  编写单元测试之前要记住的几点
4.  编写单元测试

# 单元测试的几个优点

由于单元测试有很多优点，让我们列举其中的几个:

1.  在软件开发中，变化是不变的。单元测试有助于在添加新功能时及早发现现有功能的缺陷。
2.  单元测试有助于开发人员尽早解决任何 bug，因为他们可以更好地了解正在开发的内容和已经破坏的内容，而不是等到测试团队在后期阶段报告 bug。

有了这个介绍，让我们转向用 Go 语言实现单元测试用例。

# **示例程序**

```
 type Client interface {
   GetSum(int, int) (int, error)
}type myStruct struct {
}func (input myStruct) GetSum( num1, num2 int) (int, error) {
   return num1+num2, nil
}
```

这里我们定义了一个接口`Client`，其中只定义了一个`GetSum`函数，我们还定义了一个名为`myStruct`的结构，它实现了`Client`接口。

当我们说一个结构实现了一个接口时，意思是这个结构应该实现接口中定义的所有方法，在我们的例子中是`GetSum`函数。

```
func processInput(input Client, num1, num2 int) (int, error){
   res, err := input.GetSum(num1, num2)
   if err != nil{
      return 0, err
   }
   return res, nil
}
```

这里我们定义了一个名为`processInput`的函数，它将一个`Client`接口和两个数字作为输入，并调用与输入接口相关联的`GetSum`方法。

```
func main() {
   myVariable := myStruct{}
   num1 := 1
   num2 := 2
   res, err := processInput(myVariable, num1,num2)
   if err != nil{
      panic(err)
   }else {
      fmt.Printf("Sum of %d and %d is  %d\n", num1, num2, res)
   }
}
```

最后从`main`函数我们调用`processInput`函数我们适当的参数。完整的程序可以在我的 GitHub 上找到。

# 编写单元测试时要记住的几点

在进入单元测试的实际实现之前，我们应该非常清楚以下几点:

## **1。定义一个单位的范围**

有时确定一个单元的范围变得更加困难，导致它一次测试多个单元。

要遵循的简单规则是，任何结果不在我们控制范围内的代码工作流都不要包含在单元测试中。

在我们的例子中，函数`processInput`通过[调用](https://github.com/Karthik-K-N/goUT/blob/07a5961ba7a65ceccc4e5eb4ce9de9497bbcd368/main.go#L24)到`GetSum`方法来计算总和，我们不应该为总和是如何计算的而烦恼，我们应该关注方法`GetSum`返回的结果。

## **2** 。**照顾依赖性**

如前所述，我们应该只关心外部 API 返回的结果，我们需要根据外部 API 所有可能的返回值来测试我们的程序，这就需要我们用我们想要的返回值来模拟外部 API 的行为。

在 go 语言中，这可以通过手工覆盖接口方法或使用模拟来轻松实现。当接口很大，并且提供各种内置方法来改变行为时，模拟是最适合的。

## **3。定义简单测试功能或方法**

在编写程序时，有时我们需要预见未来，以使代码易于测试

在我们的例子中，函数`[processInput](https://github.com/Karthik-K-N/goUT/blob/07a5961ba7a65ceccc4e5eb4ce9de9497bbcd368/main.go#L23)` 我们将`Client`接口作为第一个参数传递，这允许我们在后面的例子中传递任何自定义接口，该接口具有适合我们要求的方法:

```
func main() {
   myVariable := myStruct{}
   num1 := 1
   num2 := 2
   myVariable.GetSum(num1, num2)
   res, err := myVariable.GetSum(num1, num2)
   if err != nil{
      panic(err)
   }else {
      fmt.Printf("Sum of %d and %d is  %d\n", num1, num2, res)
   }
}
```

这里我们直接调用`GetSum`方法，而不是通过`processInput`计算结果。但是这种方法可以工作，但是不能提供额外的灵活性来测试我们的程序。

# 编写单元测试

在 Go 语言中，为了开始测试，我们只需要一个名为`testing`的包，尽管有许多其他包可以用来丰富测试，但是为了简单起见，让我们考虑使用`testing`包的例子。

## 简单的单元测试

首先，让我们编写一个简单的单元测试。

这里我们用 1 和 2 个输入调用`processInput`方法，并期望 3 个输出。如果返回的结果不是预期的 3，我们就没有通过测试。

```
func TestProcessInput(t *testing.T)  {
   myVariable := myStruct{}
   res, err := processInput(myVariable, 1,2)
   if err != nil {
      t.Fatal(err)
   }
   if res != 3{
      t.Fatalf("Expected result %d got %d", 3, res)
   }
}
```

## 2.**表格法单元测试**

当我们想要测试一些输入组合的功能时，上面简单的单元测试例子是很好的。

但是，当我们定义案例和输入，并迭代它们中的每一个来运行测试时，下面的测试表格方法更好。为此，我们利用了由`testing`包提供的函数`Run`。

```
func TestProcessInputTabularMethod(t *testing.T) {

   cases := []struct {
      name string
      num1 int
      num2 int
      res  int
      expectError bool
   }{
      {
         name: "Case 1",
         num1: 1,
         num2: 2,
         res:  3,
         expectError: false,
      },
      {
         name: "Case 2",
         num1: 100,
         num2: 200,
         res:  300,
         expectError: false,
      },
   }

   for _, tc := range cases {
      t.Run(tc.name, func(t *testing.T) {
         myVariable := myStruct{}
         res, err := processInput(myVariable, tc.num1,tc.num2)
         if err != nil && !tc.expectError{
            t.Fatal(err)
         }
            if res != tc.res{
               t.Fatalf("Expected result %d got %d", tc.res, res)
            }
      })
   }
}
```

## 3.**接口函数自定义方法测试**

如前所述，在编写单元测试时，我们必须注意依赖关系。我们的`processInput`函数有如下签名:

```
func processInput(input client.Client, num1, num2 int) (int, error)
```

该函数的第一个参数是`Client`接口，它提供了额外的能力，可以将任何满足`Client`接口的结构传递给该函数，因此我们可以为`Client`接口函数添加任何自定义行为来进行测试。

```
type testStruct struct {

}

func (input testStruct) GetSum( num1, num2 int) (int, error) {
   return 0, fmt.Errorf(" Intentional error ")
}

func TestProcessInputCustomMethods(t *testing.T)  {
   myVariable := testStruct{}
   res, err := processInput(myVariable, 1,2)
   if err == nil {
      t.Fatal("Expecting error! got nil")
   }
   if res != 0{
      t.Fatalf("Expected 0 found %d", res)
   }
}
```

这里我们定义了一个名为`testStruct`的结构，并针对它定义了`GetSum`方法，这个方法总是返回一个错误。这有助于测试`processInput`功能的负面情况。

## 4.**生成模拟方法**

定义自定义接口方法将有助于测试，但是当接口非常大时，我们不能花时间定义所有的方法和每个方法的各种行为。

上述问题可以通过使用模拟来解决。我们将使用包`mockgen`为我们的接口`Client`创建一个模拟方法。

```
func TestProcessInputMocks(t *testing.T){
   mockCtrl := gomock.NewController(t)
   mockClient := mock.NewMockClient(mockCtrl)

   mockClient.EXPECT().GetSum(11,2).Return(3, nil)
   //mockClient.EXPECT().GetSum(gomock.Any(),gomock.Any()).Return(3, fmt.Errorf("Intention Error "))
   //mockClient.EXPECT().GetSum(1,2).Return(33, nil)
   //mockClient.EXPECT().GetSum(1,2).Return(3, nil).Times(2)

   res, err := processInput(mockClient, 11, 2)
   if err != nil {
      t.Fatal(err)
   }
   if res != 3{
      t.Fatalf("Expected result %d got %d", 3, res)
   }
}
```

Mocks 为我们提供了各种额外的测试特性。简单地说，我们必须用期望的输入和`RETURN`值为接口函数定义`EXPECT`方法。

更多关于 mock 的特性可以在[这里](https://github.com/golang/mock)找到。