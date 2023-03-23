# 分解面向对象的 Ruby

> 原文：<https://betterprogramming.pub/breaking-down-object-oriented-ruby-cb0a79e55a70>

## 了解应用程序中模型之间的关系

![](img/fa1459d9fb24851962cdec5d83fffd76.png)

[扎克里·格罗森](https://en.wikipedia.org/wiki/User:Zacharie_Grossen)在[维基共享资源](https://commons.wikimedia.org/wiki/File:Ruby_Chocolate.jpg)

将代码分成更易管理的部分是编码最重要的部分之一。这也是最具挑战性的事情之一。Ruby 中的面向对象编程(OOP)也不例外。我分解 OO Ruby 的一般过程如下:

# 第一步

在你接触你的电脑之前，你需要了解你的对象之间的关系。出于本指南的目的，我们将看一组三个对象。我们以创业公司为例。一个创业公司可以有很多风险投资人，一个风险投资人可以支持很多创业公司。这称为多对多关系。

![](img/8c1b3c2fd12784eb13fdf1425b2364fb.png)

多对多关系

这些创业公司和风险投资家通过融资回合联系在一起。一家初创公司和一位风险资本家都可以有很多轮融资，但当风险资本家支持一家初创公司时，它是通过一轮单独的融资来实现的。这三个类之间的关系称为 has-many-through 关系。

![](img/fe96f26ab42baff1f4e4894ec2f79a87.png)

有-多-通过关系

因为创业公司和风险投资家之间的多对多关系是通过融资轮存在的，融资轮就是加入者。

# 第二步

现在我们已经确定了加入者，我们可以开始构建我们的类了。构建类时，联接器总是最后构建的。我们将开始为我们的启动声明如下的类:

```
class Startup
end
```

接下来，我们将添加一些基本信息和初始化方法。一家初创公司用名称、创始人和域名进行初始化，因此我们的类将包含以下内容:

```
class Startup
  @@all = []    
  attr_accessor :name     
  attr_reader :founder, :domain def initialize(name, founder, domain)        
    @name = name        
    @founder = founder        
    @domain = domain        
    @@all << self    
  end
end
```

请注意，创业公司的名称可以更改，但其域名和创始人不能更改。名字会写一个`attr_accessor`，创始人和域名会写一个`attr_reader`。同样，这个类是用一个空的`@@all array`声明的，initialize 方法将每个类实例添加到这个数组中。这允许我们跟踪我们创建的所有实例。

既然我们已经有了创业类的基本信息，让我们为风险资本家类建立基本信息。一个风险资本家实例用一个`name`和一个`total_worth`初始化，两者都可以改变，所以我们的基本风险资本家类将如下所示:

```
class VentureCapitalist 
  @@all = [] 
  attr_accessor :name, :total_worth

  def initialize(name, total_worth) 
    @name = name 
    @total_worth = total_worth 
    @@all << self 
  end
end
```

# 第三步

现在我们已经为其他类构建了基础，让我们为 joiner 做同样的事情。

joiner 类是用类型和投资初始化的，这两者都可以改变。joiner 类必须知道它要加入的类，所以它将用每个其他类的实例变量进行初始化。

当风险资本家支持一家初创公司时，他们可以想投资多少就投资多少，但他们不能从初创公司拿钱。为了确保正确初始化投资，我们将包含一个 if 语句，该语句将自动确保投资不会小于零。

joiner 类的基础如下:

```
class FundingRound 
  @@all = [] 
  attr_accessor :type, :investment 
  attr_reader :startup, :venture_capitalist   def initialize(startup, venture_capitalist, type, investment)
    @startup = startup 
    @venture_capitalist = venture_capitalist 
    @type = type     if investment < 0 
      @investment = 0 
    else 
      @investment = investment.to_f 
    end 
    @@all << self 
  end
end
```

# 第四步

现在我们有了类的基本信息，我们可以开始创建使用这些信息的方法。我建议首先编写只涉及一个类的所有方法，然后编写涉及多个类的方法。我们将编写的第一个方法如下:

```
def self.all        
  @@all    
end
```

这是一个重要的方法，它允许我们访问一个类的所有实例，这对构建其他方法很有用。我们会给每个类这个方法。

假设我们想找一家以创始人名字命名的创业公司。为此，我们将遍历`@@all array`，直到找到创始人与我们的创始人姓名相匹配的初创公司。该方法将如下所示:

```
def self.find_by_founder(founder_name)        
    self.all.find do |startup|            
      startup.founder == founder_name        
    end    
end
```

现在，让我们假设我们想要得到我们创业公司的所有领域。为此，我们将遍历`@@all array`并使用`map`创建一个启动域数组。

```
def self.domains        
  self.all.map do |startup|            
    startup.domain        
  end    
end
```

# 第五步

现在我们将学习如何处理多个类。

假设我们想知道一家初创公司已经获得的所有资金。启动类不跟踪该信息。相反，是融资轮类在跟踪这些信息，所以要获得初创公司的总资金，我们需要查看它的融资轮数。

在我们能够计算出创业公司已经收到的资金之前，我们需要知道属于创业公司的所有融资轮次。这是一条重要的信息，它可以帮助我们获得更多关于初创公司融资轮次和投资该公司的风险资本家的信息。

为此，我们将遍历所有融资轮次，并选择与我们正在寻找的初创公司名称相匹配的实例。接下来，我们将遍历结果数组，以确定创业公司每轮融资的投资额。然后，我们将对所有这些在我们的创业公司中进行的投资求和，得到创业公司的总资金。我们的 helper 方法和 total_funds 方法如下:

```
def funding_rounds 
  FundingRound.all.select do |round|            
    round.startup == self        
enddef total_funds             
  investments = funding_rounds.map do |round|            
    round.investment        
  end        
  investments.sum    
end
```

现在，假设我们想知道所有支持创业公司的风险投资家。初创公司不会跟踪支持它的风险资本家，但因为每一轮融资都会跟踪初创公司和风险资本家，所以我们可以使用融资轮来获得这些信息。为此，我们将再次创建一个助手函数，但这一次，当我们遍历初创公司的所有融资轮次时，我们将映射风险资本家。这种方法如下:

```
def investors        
  funding_rounds.map do |round|            
    round.venture_capitalist        
  end.uniq    
end
```

现在让我们假设我们的风险投资者想要了解他们所支持的创业公司。风险资本家类不跟踪有关其初创公司的信息，但因为融资轮类跟踪初创公司和风险资本家，我们可以使用融资轮来获得这些信息。

为此，我们将首先构建一个助手方法，类似于我们为初创公司编写的 funding_rounds 助手方法。这一次，我们将获得与风险投资家匹配的所有融资轮次，而不是寻找与初创公司匹配的融资轮次。然后，我们将使用这个助手方法来映射风险投资家在他们的融资轮中支持的初创公司。这些方法编写如下:

```
def funding_rounds        
  FundingRound.all.select do |round|              
    round.venture_capitalist == self        
  end    
end def portfolio        
  self.funding_rounds.map do |round|            
    round.startup        
  end.uniq    
end
```

# 第六步

现在我们已经构建了所有三个类，我们可以获得每个类的信息，也可以获得相关类的信息。我们必须做的最后一件事是测试，以确保我们已经正确地创建了一切。

为此，我们从创建种子数据开始，这是一组我们可以试验的类实例的例子。为了测试我们的代码，我们可以简单地进入终端并运行我们的文件。

然后我们可以调用我们的种子数据，并在我们的类和种子数据上调用我们的方法。如果测试数据和方法产生了我们期望的结果，我们知道我们已经正确地编写了代码。