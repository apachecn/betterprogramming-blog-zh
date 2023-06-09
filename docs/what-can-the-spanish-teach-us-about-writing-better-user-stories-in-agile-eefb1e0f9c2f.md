# 关于在敏捷中编写更好的用户故事，西班牙人能教我们什么

> 原文：<https://betterprogramming.pub/what-can-the-spanish-teach-us-about-writing-better-user-stories-in-agile-eefb1e0f9c2f>

## 你知道这三个朋友吗？

![](img/134071fee6de16b8e0806365b8b1cb76.png)

丹尼尔·普拉多在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

别担心。这并不意味着我们必须用西班牙语写我们的故事！相反，我们将探索一种敏捷的工作方式，三个朋友。

我们将通过一个会话如何帮助团队更好地理解需求的例子来发现什么是三个朋友。

# 三个朋友是什么？

“三个朋友”，在英语中翻译成“三个朋友”，是一种敏捷的工作方式，三个观点不同的人聚在一起提炼用户故事。

正如[敏捷联盟](https://www.agilealliance.org/glossary/three-amigos)所描述的，这三个视角包括:

*   商业——我们试图解决什么问题？
*   发展——我们如何构建解决方案来解决这个问题？
*   测试——这个呢？会发生什么？

因此，当提炼用户故事时，开发人员、测试人员和业务分析师(BA)应该在场，以充分理解需求。

# 三个朋友是如何工作的？

让我们看一个例子，一个团队在三个朋友的会议中讨论一个用户故事。

*团队坐在一起，对下面的用户故事进行提炼:*

```
As an employee, I want to submit my timesheet so I can get paid
```

开发人员:那么，我们需要在时间表上捕捉什么呢？

BA:我们需要获取一个日历月中每天工作的小时数。

开发商:完全没问题。我们可以创建一个表单，用户可以输入他们每天的工作时间。然后，当用户点击提交时，我们是否可以向 HR 发送电子邮件来处理时间表。

巴:不幸的是，一封电子邮件并不能解决问题。我们需要将这些信息发送到我们的人力资源系统，因为我们有一个自动化的工资单流程。

开发者:好的，这样的话，我们可以通过 HR API 发送给 HR 系统。

测试员:如果他们输入不正确的信息会怎么样？

开发者:我们可以向用户提供反馈。例如，当用户提供无效数据时，我们可以显示一条验证消息。

测试人员:什么是无效数据？用户可以输入负数的工作时间吗？一个用户一天最多可以工作多少小时？

BA:不，用户应该只能输入 12 以内的正数。

测试人员:好的，我们应该写一个测试用例来涵盖这一点。

开发人员:我也会确保我们对小时数有适当的确认。

BA:还有，我刚想起来我们需要发一封电子邮件通知给直线经理批准。

开发人员:所以我们需要确保在“审核”状态下提交时间表，这样员工直到他们的直线经理批准后才能拿到工资？

巴:正确。

测试员:直线经理不在怎么办？

巴:这个问题问得好。我们还应该给直线经理 b 发邮件。

测试人员:如果直线经理想要拒绝提交的时间表怎么办？

开发者:这将会是大量的开发工作。我可以建议我们为这些额外的场景创建新的用户故事吗？

*团队同意用户故事现在相当大，所以将电子邮件通知工作分成单独的用户故事，以便以后改进:*

```
As a line manager, I want to get notified if one of my staff submit a timesheet so I can review itAs a line manager, I want to be able to approve my staff timesheets so I can ensure my staff get paidAs a line manager, I want to be able to reject a timesheet so I can ensure my staff aren't paid incorrectly
```

开发人员:为了获取时间表的细节，我们还需要考虑什么吗？

巴:我想就这些了。如果需要的话，我们可以在将来重复这个过程。

*团队进入下一个故事。*

# 团队从这次会议中发现了什么？

对于这个单一用户案例，团队发现了以下情况:

1.  首先，必须记录一个月中每天的工作小时数。
2.  第二，工作小时数必须是正数，最多十二小时。
3.  第三，必须对表单进行验证，并向用户提供反馈。
4.  最后，当用户提交他们的时间表时，系统必须将数据以审阅状态发送给 HR API。

除此之外，该团队还创建了三个新的用户故事。

他们现在更好地准备评估和承担这项工作。

# 最佳实践

使用三个朋友来提炼用户故事没有对错之分。相反，我建议尝试一下，找出最适合你的团队的方法。然而，这里有一些好的做法可以帮助你:

1.  时间盒会议——时间盒有助于团队聚焦；如果你没有提炼完一个故事，你可以随时回到这个故事。45 分钟到 1 小时通常是一个很好的经验法则。
2.  每次会议轮换团队成员——这有助于在团队中分享知识。
3.  每个领域有一名成员——不同的视角提供了额外的价值。
4.  不要着急，需要多长时间就有多长时间。预先提炼故事意味着我们在开发时不会问那么多问题。

# 外卖食品

从上面的例子，我们可以看到三个朋友的价值。

开发人员帮助确定了问题的解决方案。

测试人员通过强调潜在的问题来提供价值。

英国航空公司当场就提供了信息，因此团队不必离开去寻找他们迫切需要的答案。

团队现在更有信心在下一次冲刺中承担这个故事！