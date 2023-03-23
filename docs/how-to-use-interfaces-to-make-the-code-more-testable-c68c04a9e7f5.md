# 如何使用接口使代码更易测试

> 原文：<https://betterprogramming.pub/how-to-use-interfaces-to-make-the-code-more-testable-c68c04a9e7f5>

## 伟大的界面——第 3 部分

![](img/ad0715d5abcfc7dd1010d8aea763d037.png)

[图片来自 Pixabay](https://pixabay.com/vectors/checklist-check-marketing-project-154274/)

# 介绍

编程到接口而不是实现。这一条规则可以给编程带来很多好处。本文将展示该接口如何通过重构来提高代码的可测试性。当我们使用接口时，我们可以在测试过程中用一个模拟对象替换实际对象，并且我们可以用任何我们想要的方式来模拟它，以达到软件测试的目的。

> 这是我关于伟大界面文章的最后一部分。请在下面找到前两篇文章的链接:
> 
> 第 1 部分:它涵盖了界面的基础知识。
> [第二部分](https://cullensun.medium.com/the-great-interface-part-2-84e0ee73ceb2):主要讲利用接口实现一些常见的设计原则和设计模式。

# 重构前

请看下面一个小小的订房 app。它允许用户通过他们的电子邮件和预期日期来预订房间。如果预订成功，系统将更新数据库并向用户发送电子邮件。

## `**DataStoreService.java**`

这是一个连接到数据库并更新数据的类。真实世界的数据库访问将更加复杂，并且可能是异步的。然而，我简化了它来演示接口的概念。

```
package com.great.refactor.before;

public class DataStoreService {
    public DataStoreService() {
        System.out.println("set up database connection");
    }

    public boolean markDateAsBooked(String date) {
        System.out.printf("write into database to book the date %s\n", date);
        return true;
    }
}
```

## **EmailService.java**

该电子邮件服务将帮助我们向用户发送具有给定主题和内容的电子邮件。

```
package com.great.refactor.before;

public class EmailService {
    public EmailService() {
        System.out.println("configure and setup up email connection");
    }

    public boolean sendEmail(String receiver, String subject, String content) {
        System.out.printf("send email to %s \nsubject: %s \ncontent: %s\n", receiver, subject, content);
        return true;
    }
}
```

## **Booking.java**

这才是真正的预订应用本身。它有一个名为`processBooking`的主要方法，依靠一个`DataStoreService`实例和一个`EmailService`实例来完成部分工作。

```
package com.great.refactor.before;
import java.time.LocalDate;

public class Booking {
    public enum Result {
        FAILURE,
        PARTIAL_SUCCESS,
        SUCCESS
    }

    private DataStoreService dataStore = new DataStoreService();
    private EmailService emailService = new EmailService();

    private String getEmailSubject() {
        return "You have successfully booked the function room";
    }

    private String getEmailContent() {
        return "Thank you. See you soon.";
    }

    public Result processBooking(String userEmail, LocalDate date) {
        String dateStr = date.toString();
        boolean bookingSuccess = dataStore.markDateAsBooked(dateStr);
        if (!bookingSuccess) {
            return Result.FAILURE;
        }

        // Just simple illustration here. It might be some complicated logics in real application.
        String emailSubject = getEmailSubject();
        String emailContent = getEmailContent();
        boolean sendingEmailSuccess = emailService.sendEmail(userEmail, emailSubject, emailContent);
        if (!sendingEmailSuccess) {
            System.out.println("Email server down. Need to alert user.");
            return Result.PARTIAL_SUCCESS;
        } 

        return Result.SUCCESS;
    }

    public static void main(String[] args) {
        Booking booking = new Booking();
        booking.processBooking("xyz@abc.com", LocalDate.now());
    }
}
```

## **测试**

我们打算对`Booking`类进行单元测试。不幸的是，当前的实现使得测试变得不可能，因为依赖关系是如此紧密地耦合在一起。另一方面，在测试环境中，真实的数据库通常是不可访问的，在单元测试中它不应该发送真实的电子邮件。让我们继续看看如何重构代码，使其可测试。

# 重构后

## **DataStoreService.java**

现在我们有了`DataStoreService`作为接口，我们有了一个叫做`DataStoreImplementation`的类。

```
package com.great.refactor.after;

public interface DataStoreService {
    public boolean markDateAsBooked(String date);
}

class DataStoreImplementation implements DataStoreService {
    public DataStoreImplementation() {
        System.out.println("set up database connection");
    }

    @Override
    public boolean markDateAsBooked(String date) {
        System.out.printf("write into database to book the date %s\n", date);
        return true;
    }
}
```

## **EmailService.java**

类似地，我们也有一个接口和一个对`EmailService`的实现。

```
package com.great.refactor.after;

public interface EmailService {
    public boolean sendEmail(String receiver, String subject, String content);
}

class EmailImplementation implements EmailService {
    public EmailImplementation() {
        System.out.println("configure and setup up email connection");
    }

    @Override
    public boolean sendEmail(String receiver, String subject, String content) {
        System.out.printf("send email to %s \nsubject: %s \ncontent: %s\n", receiver, subject, content);
        return true;
    }
}
```

## **Booking.java**

“预订”现在依赖于接口而不是具体的类，依赖关系可以用构造函数提供(注入)。

```
package com.great.refactor.after;
import java.time.LocalDate;

public class Booking {
    public enum Result {
        FAILURE,
        PARTIAL_SUCCESS,
        SUCCESS
    }

    private DataStoreService dataStore;
    private EmailService emailService;

    public Booking(DataStoreService dataStore, EmailService emailService) {
        this.dataStore = dataStore;
        this.emailService = emailService;
    }

    private String getEmailSubject() {
        return "You have successfully booked the function room";
    }

    private String getEmailContent() {
        return "Thank you. See you soon.";
    }

    public Result processBooking(String userEmail, LocalDate date) {
        String dateStr = date.toString();
        boolean bookingSuccess = dataStore.markDateAsBooked(dateStr);
        if (!bookingSuccess) {
            return Result.FAILURE;
        }

        // Just simple illustration here. It might be some complicated logics in real application.
        String emailSubject = getEmailSubject();
        String emailContent = getEmailContent();
        boolean sendingEmailSuccess = emailService.sendEmail(userEmail, emailSubject, emailContent);
        if (!sendingEmailSuccess) {
            System.out.println("Email server down. Need to alert user.");
            return Result.PARTIAL_SUCCESS;
        } 

        return Result.SUCCESS;
    }

    public static void main(String[] args) {
        Booking booking = new Booking(new DataStoreImplementation(), new EmailImplementation());
        booking.processBooking("xyz@abc.com", LocalDate.now());
    }
}
```

# **模仿**

现在测试变得容易多了。我们可以创建如下所示的模拟类。请注意，我们可以任意操纵模拟类的行为，只要它们实现了接口。看看模拟类中的变量名。以“shall”为前缀的布尔变量(如`shallBookSuccess`)将用于操纵模仿行为。而前缀为“updated”的变量将用于捕获调用参数以进行验证。

```
class MockDataStoreImplementation implements DataStoreService {
    String updatedDate;

    boolean shallBookSucceed;

    @Override
    public boolean markDateAsBooked(String date) {
        updatedDate = date;
        return shallBookSucceed;
    }
}

class MockEmailImplementation implements EmailService {
    String updatedReceiver;
    String updatedSubject;
    String updatedContent;

    boolean shallEmailSucceed;

    @Override
    public boolean sendEmail(String receiver, String subject, String content) {
        updatedReceiver = receiver;
        updatedSubject = subject;
        updatedContent = content;
        return shallEmailSucceed;
    }
}
```

# **测试**

请看看下面的测试。我们测试程序的每一条逻辑路径，以确保行为符合预期。

例如，在第一个测试`booking_dataStoreFail`中，我们设置数据存储失败。我们通过检查`updatedDate`来检查`dataStoreService`是否被调用，并且通过检查那些更新的变量是否为空来验证`emailService`是否从未被调用。最后，我们还检查结果应该是`FAILURE`。

```
class MockEmailImplementation implements EmailService {
    String updatedReceiver;
    String updatedSubject;
    String updatedContent;

    boolean shallEmailSucceed;

    @Override
    public boolean sendEmail(String receiver, String subject, String content) {
        updatedReceiver = receiver;
        updatedSubject = subject;
        updatedContent = content;
        return shallEmailSucceed;
    }
}

public class BookingTest {
    LocalDate getTestingDate() {
        return LocalDate.of(2022, 11, 14);
    }

    @Test
    public void booking_dataStoreFail() {
        // Given
        MockDataStoreImplementation dataStoreService = new MockDataStoreImplementation();
        dataStoreService.shallBookSucceed = false;
        MockEmailImplementation emailService = new MockEmailImplementation();
        emailService.shallEmailSucceed = true;
        Booking booking = new Booking(dataStoreService, emailService);

        // When
        Booking.Result bookingResult = booking.processBooking("xyz@abc.com", getTestingDate());

        // Then
        assertEquals(dataStoreService.updatedDate, "2022-11-14");
        assertEquals(emailService.updatedReceiver, null);
        assertEquals(emailService.updatedSubject, null);
        assertEquals(emailService.updatedContent, null);
        assertEquals(bookingResult, Booking.Result.FAILURE);
    }

    @Test
    public void booking_dataStoreSuccess_emailFail() {
        // Given
        MockDataStoreImplementation dataStoreService = new MockDataStoreImplementation();
        dataStoreService.shallBookSucceed = true;
        MockEmailImplementation emailService = new MockEmailImplementation();
        emailService.shallEmailSucceed = false;
        Booking booking = new Booking(dataStoreService, emailService);

        // When
        Booking.Result bookingResult = booking.processBooking("xyz@abc.com", getTestingDate());

        // Then
        assertEquals(dataStoreService.updatedDate, "2022-11-14");
        assertEquals(emailService.updatedReceiver, "xyz@abc.com");
        assertEquals(emailService.updatedSubject, "You have successfully booked the function room");
        assertEquals(emailService.updatedContent, "Thank you. See you soon.");
        assertEquals(bookingResult, Booking.Result.PARTIAL_SUCCESS);
    }

    @Test
    public void booking_dataStoreSuccess_emailSuccess() {
        // Given
        MockDataStoreImplementation dataStoreService = new MockDataStoreImplementation();
        dataStoreService.shallBookSucceed = true;
        MockEmailImplementation emailService = new MockEmailImplementation();
        emailService.shallEmailSucceed = true;
        Booking booking = new Booking(dataStoreService, emailService);

        // When
        Booking.Result bookingResult = booking.processBooking("xyz@abc.com", getTestingDate());

        // Then
        assertEquals(dataStoreService.updatedDate, "2022-11-14");
        assertEquals(emailService.updatedReceiver, "xyz@abc.com");
        assertEquals(emailService.updatedSubject, "You have successfully booked the function room");
        assertEquals(emailService.updatedContent, "Thank you. See you soon.");
        assertEquals(bookingResult, Booking.Result.SUCCESS);
    }
}
```

# 摘要

如果我们对接口编程，测试就变得容易多了。上面显示的重构过程将不可测试的软件变成了完全可测试的。我还提到了像模仿和依赖注入这样的概念，它们对于测试是必不可少的。

希望对你有用。你可以在 GitHub 上找到这个[项目](https://github.com/CullenSUN/great_interface/tree/master/src/main/java/com/great/refactor)的完整源代码。