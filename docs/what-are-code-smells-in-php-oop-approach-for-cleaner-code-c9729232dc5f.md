# PHP 中有哪些代码气味？一种更简洁代码的面向对象方法

> 原文：<https://betterprogramming.pub/what-are-code-smells-in-php-oop-approach-for-cleaner-code-c9729232dc5f>

## 识别代码气味并编写更干净、更安全的代码的 7 个技巧，并附有示例

![](img/1d615d06cabb64f13c301e31b5fbeae6.png)

# 动机

许多程序员很快就对他们的代码获得了太多的自信，他们认为“这就是我的做法”他们觉得无视风险很好。我的方法是遵循尽可能多的客观规则和最佳实践。几十年来，编程一直在改进，并且有许多经过验证的伟大概念。在输入第一行代码之前，我们节省了大量的学习时间。实际上，仅仅几个基本的 OOP 规则就能帮助我们写出更好(更安全)的代码——Google SOLID、DRY、SoC、模式和反模式。

**代码气味**是一个描述可疑代码构造的术语。这些本身可能不是错误或缺陷，但从面向对象编程(OOP)的角度来看，很可能是有问题的。在本文中，我们将重点介绍最常见的错误，并使用简单的示例展示如何改进代码。

# 1.功能描述中的 and 太多

每当你描述你的课程目的时，注意你使用*和*的次数。随着代码的增长，我们很自然地倾向于将太多的函数放在一个类中，或者将太多的功能放在一个方法中。

大类的问题在于它们难以理解，难以测试，并且极难扩展。记住 OOP 中最重要的原则之一是开闭原则[ [1](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) ]。类对于修改应该是封闭的，但是对于扩展应该是开放的。任何做得太多的类都很难在保持复杂的原始功能不变的情况下进行扩展。

## **例子**

我不是 Laravel 的 ORM 系统雄辩的粉丝，因为 Laravel 的模型做得太多了。它们携带数据，**和**与数据库通信，**和**将数据库数据映射到模型属性，**和**处理事件等。一个班的责任太多了。

更好的方法可能是将责任分成以下几层:

*   **控制器** —验证输入数据
*   **服务** —包含业务逻辑
*   **型号** —携带数据
*   储存库 —为数据库准备数据
*   **DBMapper** —使用 ModelMap 将模型映射到数据库行，model map 知道如何将模型属性正确映射到数据库列

(我们在我们的 ***LayerFrame*** 模块中使用这种方法用于 Laravel，这里描述的。)

传入的请求模型不应被仓库或数据库映射器重用。它们应该被认为是控制者的唯一属性。通过将这些模型映射到业务模型来进行任何进一步的处理，反之亦然。

# 2.类中的“New”关键字

每当你在一个类里面看到 *new* 的时候，很可能就是一股代码味。在另一个类中创建一个类会在这两个类之间产生非常紧密的耦合。很难单独测试这些类，因为你不能改变内部类的行为。另外，如果您需要扩展功能，您将很难安全地实现它。

## **例子**

```
class WebSearch
{
   protected $httpClient;

   public function __constructor()
   { 
       $this->httpClient = new HttpClient();
   }
}
```

这是一个典型的例子——如何在两个或多个类之间创建紧密耦合。在另一个对象中使用一个对象的正确方法是将它传递给构造函数。

```
class WebSearch
{
   protected $httpClient; public function __constructor(HttpClient $httpClient)
   {
      $this->httpClient = $httpClient;
   }
}
```

这是正确的 OOP 方式，它为测试、扩展和可读性带来了很多好处。如果你使用任何带有依赖注入容器的框架(比如 Laravel)，它可以自动为你的类提供所有的依赖。这就是为什么你看不到 Laravel 项目中正确编写的 *new* 关键字出现的次数。

# **3。违反*告诉、不问*原则**

面向对象编程教给我们关于类的知识。起初可能不清楚如何从外部使用内部类数据。让类提供数据、编辑数据并放回数据似乎是很自然的事情。例如:

```
class User
{
   public $physicalData = [];
   public $age = 0;
}$user = new User();user->physicalData['height'] = 180;
user->physicalData['weight'] = 85;$user->age++;
```

这样的方法看似直观，但在很多情况下会带来很多问题。这是一个记住“*告诉，不要问*原则时得心应手的情况。对于我们的类，规则意味着**我们不应该向类请求数据**，然后在类外操纵它们。最好是**告诉学生如何处理数据**。以下是上述示例的解决方案:

```
class User
{
   private $physicalData = [];
   private $age = 0; public function increaseAge()
   {
      $this->age++;
   } public function getAge()
   {
      return age;
   } public function setPhysicalData(string $parameter, $value)
   {
      $this->physicalData[$parameter] = $value;
   } public function getPhysicalData($parameter)
   {
      return array_key_exists($parameter, $this->physicalData)
         ? $this->physicalData[$parameter]
         : null;
   }
}$user = new User();user->setPhysicalData('height', 180);
user->setPhysicalData('weight', 85);$user->increaseAge();
```

在第二个例子中，我们告诉这个类为我们操作数据。遵循这条规则有很大的好处。我们的类很可能保持接口不变。类客户机不应该关心内部结构。例如，客户希望存储一些数据，如体重、身高等。它不需要知道，类实际上是如何处理数据的。它可以是类、数组、单独的变量等。它甚至可能是磁盘上的一个文件。

为了让上面的例子更好，我们可以把数据分成单独的变量。它将允许我们引入类型安全，并更明确地说明函数的作用。

```
class User
{ private $height;
   private $weight; public function setHeight(int $height)
   {
      $this->height = $height;
   } public function setWeight(float $weight)
   {
      $this->weight = $weight;
   }

    … getters and other code
}
```

# **4。代码难以阅读**

我总是告诉我的客户，他们通常不是程序员，他们应该能够阅读一些代码。要么程序员使用大量注释，要么他/她用描述性命名将代码分割成多个函数。

## **举例**

比较这两个版本的代码，并回答自己哪一个更容易阅读。

```
public function login($email, $user)
{
   …
   if(preg_match("/^([a-z0–9\+_\-]+)(\.[a-z0–9\+_\-]+)*@([a-z0–   9\-]+\.)+[a-z]{2,6}$/ix", $email) {
      Auth::login($user);
   }
   …
}
```

或者

```
public function login($email, $user)
{
   …
   if($this->isEmailValid()) { 
      Auth::login($user);
   } 
   …
}private function isEmailValid($email) : bool
{
    return (preg_match("/^([a-z0–9\+_\-]+)(\.[a-z0–9\+_\-]+)*@([a-  z0–9\-]+\.)+[a-z]{2,6}$/ix", $email));
}
```

我们可能觉得创建短函数毫无意义。为什么不把代码放到主函数里？然而，拆分代码可以提高可读性——不仅对您的团队成员和测试人员，甚至对您自己，当您在几个月后查看代码时。

## **举例**

通常，我们按照自己的想法编写代码行，我们没有给这个想法更多一点的思考。写这样的东西是很常见的:

```
public function login($email, $user)
{
   if($this->isEmailValid()) {
      if(!$this->registerUser($email)) {
        echo('Registration error');
      }
   } return 'Error';
}
```

我见过有 5-7 层嵌套的 *if* 、 *foreach* 、 *while、*以及更多*if*的代码。对于如此复杂的嵌套结构，我们可以得出结论，不可能在合理的时间内理解它。但是，很多时候，我们可以简单地通过使用否定逻辑来有效地减少嵌套条件的数量。

我们可以这样写上面的例子:

```
public function login($email, $user)
{
   if(!$this->isEmailValid()) {
      return 'Error';
   } if(!$this->registerUser()) {
      echo('Registration error');
   }
}
```

通过简单地将第一个条件改为否定条件，我们消除了一个嵌套层次。代码现在更容易阅读了。

# **5。复制并粘贴**

每当你想使用复制和粘贴，立即停止点击和思考。真的需要复制代码吗？没有更好的办法吗？代码可重用性是 OOP 的主要优点之一。干原则——不要重复自己——应该永远在我们的脑海里。

我见过一个大约有 30 页的网站，每一页都有相同的 HTML 标题。如果客户决定改变图标会发生什么？嗯，程序员需要编辑 30 个文件！任何手工工作都很容易遗漏一些东西。几乎可以肯定的是，在编辑 30 个页面的时候，有些事情让你分心，并且有一个或多个页面没有更新。只需创建一个单独的头文件，并将其包含到所有其他文件中！

# **6。代码太长**

程序员经常边想边写代码。这是一种自然的编码方式，会导致冗长的函数和杂乱无章的代码。三个简单的提示会让你的代码更容易阅读。首先，不要写比你的显示器高度更长的函数。简单地说，如果你需要在一个单独的函数中滚动，它很可能太长了。第二，避免嵌套循环和条件。如果你需要三层以上的嵌套，代码需要重新考虑。它很可能会写得更好。第三，功能描述不要用“*和*”或者至少不要用太多。

最近，我看到了一个接收输入数据、验证输入数据、创建模型、存储模型并发送通知邮件的功能。太多的“*和*”。还记得 SRP——T2 的单一责任原则吗？

# **7。数组代替类**

当我们写代码时，我们倾向于使用琐碎的解决方案来节省时间，即使从 OOP 的角度来看它们不是最好的。其中一个直观的概念是使用数组，以防我们需要返回更多的值。像这样:

## **例子**

```
public function login($email)
{
   if(!$this->isEmailValid()) { 
      return [
        'error' => true,
        'message' => 'Invalid email',
        'code' => 400
     ];
  }
}
```

PHP 不关心数组包含什么。它可以是任何东西——数字、字符串、类、任何东西的另一个数组。这是另一种代码气味。如果一个数组包含几个不同的类型，它可能应该是一个类。用来代替类的数组极难扩展。它们不允许任何功能，只是携带一些数据。

在我们的示例中，我们可能希望测试结果是否有如下错误:

```
$loginStatus = $this->login('[my@email.com](mailto:my@email.com)');if($loginStatus->haserror()) {
   …do something
}
```

因为数组无法做到这一点，所以我们将被迫像这样始终测试特定的数组项:

```
$loginStatus = $this->login('[my@email.com](mailto:my@email.com)');if($loginStatus['error']) {
  …do something
}
```

该数组的项目现在不能被重命名，它实际做什么可能不明显，我们有非常有限的扩展选项。除此之外，每一次更新错误对象的尝试都将是一次真正的痛苦。我们甚至可能在整个代码中有几个不同的错误对象。

我的经验告诉我，每当我掉进这个“*它只是一个简单的数组*陷阱，它很快就会在背后踢我一脚。我的建议是不要懒于创建简单的类。我们让代码更容易阅读，遵循*开-关原则*，我们无疑为将来节省了大量工作。

你在编码中遵循的一些最佳实践是什么？分享在评论里！