# Salesforce 中 FFLib 的模拟数据

> 原文：<https://betterprogramming.pub/mocking-data-for-fflib-in-apex-for-salesforce-a8f0453c1a9>

## 通过模仿 FFLib Apex 选择器的数据来加速您的单元测试！

![](img/5d3dd42a751e6cb8f31af8dd53ea3784.png)

## 为什么我们要模仿我们的 FFLib Apex 单元测试？

我们想要保持我们的心流状态，保持心流状态的一个原则是获得持续清晰和即时的反馈。在软件开发中，我们很幸运拥有结构化的方法来创建可测试的代码块，从而保证我们的安全。

最后一段最重要的部分是这个词:*“立即”。*这些测试运行得越快，越容易，你的开发体验就会越好。

如果您想在进入 FFLib mocking checkout 之前了解 Apex 中模拟数据概念的更多信息，请访问:

[](https://trailhead.salesforce.com/en/content/learn/modules/unit-testing-on-the-lightning-platform/mock-stub-objects) [## Lightning 平台上的单元测试

### 解释什么是模拟和存根。描述何时使用模拟和存根。使用模拟和存根编写单元测试…

trailhead.salesforce.com](https://trailhead.salesforce.com/en/content/learn/modules/unit-testing-on-the-lightning-platform/mock-stub-objects) 

## 什么时候该嘲笑，什么时候不该嘲笑！

简单的回答是，我们模仿使用 [FFLib Apex 公共](https://github.com/apex-enterprise-patterns/fflib-apex-common)库进行 DML 操作的选择器。

在我们的组织中，我们有两种类型的 Apex 测试:

*   **单元测试:**
    对于领域逻辑，这些不与数据库交互，相反我们模拟了 a 选择器，这意味着它们只测试方法的逻辑。这些通常用于控制器类或服务类中的方法。
*   **集成测试:**
    用于使用 DML 操作与数据库交互的方法，或者用于获取对象模式。这些通常用于选择器方法，但有时也包括对象模式的服务方法。

## 创建全局日期

为您的测试建立一个全局数据或`DateTime`是很好的。

如果您设置了一个全局的`dateTime`，那么您知道当您的测试运行时，您的时间将是一致的。如果您的测试在午夜前一分钟开始运行，然后在第二天运行测试集中的测试，这将非常有用。这在过去已经引起了问题，所以为了防止问题，设置一个全球测试`dateTime`或日期:

```
static Date todaysDate = Date.today();
```

## 首先设置测试数据

已保存在数据库中的 Salesforce 对象和未保存在数据库中的 sales force 对象之间的区别很简单—如果它已保存在数据库中，则它有一个 Id。

`FFLib`有一个很好的模仿对象 Id 的方法，所以它可以像一个已经保存到数据库的对象一样被处理，即使它还没有保存，因此使它成为一个模仿对象。

要创建模拟对象，我们使用以下方法:

```
fflib_IDGenerator.generate(sObject.SObjectType);
```

这在初始化时给了它一个 Id，即使它只存在于内存中。我们现在用一些模拟数据建立了我们的测试:

## 让我们创建测试

为了创建测试，我们将使用以下模式:

*   建立
*   创建模拟
*   考虑到
*   当...的时候
*   然后

**设置**

在列表中创建测试对象的实例:

```
Contact testContact = testContact();
List<Contact> testContactsList = new List<Contact> {testContact};
Set<Id> testContactsSet = new Set<Id>{testContact.Id};
```

**创建模拟场景**

设置模拟选择器:

```
fflib_ApexMocks mocks = **new** fflib_ApexMocks();ContactSelector contactSelectorMock = 
(ContactSelector) mocks.mock(ContactSelector.class);
```

**赐**

测试模拟以返回模拟数据:

```
mocks.startStubbing();mocks.when(contactSelectorMock.sObjectType())
.thenReturn(Contact.SObjectType);mocks.when(contactSelectorMock.selectById(testContactsSet))
.thenReturn(testContactsList);mocks.stopStubbing();Application.Selector.setMock(contactSelectorMock);
```

**当**

运行测试并获得结果:

```
*Test*.startTest();List<Contact> result = ConatctController.getContacts(contactId);*Test*.stopTest();
```

**然后**

验证模拟选择器是否工作，并检查方法是否返回正确的结果:

```
((ContactSelector) mocks.verify(contactSelectorMock)).selectById(testContactsSet);*System*.assertEquals(1, result.size(), 'Check that the record returned.');*System*.assertEquals(testContactsList[0].Id, result[0].Id, 'Check correct Contact Id returned.');System.assertEquals( 9  todaysDate,  10  result[0].Date_Created__c,  11  'Check record created on the correct date.' 12);
```

## 把它们放在一起

这是一个利用 FFLib 模拟的单元测试的代码示例:

## 最后的想法

单元测试拯救生命！这有点夸张，但它们可以节省代码。当编写单元测试时，请记住，你不只是希望获得 100%的代码覆盖率，这是你应该追求的目标，单元测试应该以测试逻辑的方式编写。确保你写的测试不仅仅是测试代码，而是测试逻辑。

如果你真的很勇敢，你可以采取测试驱动的设计方法:

在创建逻辑的同时编写测试。有了模拟数据并知道方法的结果，就没有什么可以阻挡你了！

祝你嘲讽好运。

> 参考

*   [https://andyinthecloud . com/2016/06/26/与 apex-mocks-matchers-and-unit-of-work/](https://andyinthecloud.com/2016/06/26/working-with-apex-mocks-matchers-and-unit-of-work/)
*   [https://github . com/apex-enterprise-patterns/fflib-apex-common](https://github.com/apex-enterprise-patterns/fflib-apex-common)