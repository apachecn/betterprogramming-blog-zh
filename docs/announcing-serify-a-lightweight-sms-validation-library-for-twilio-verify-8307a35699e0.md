# 宣布 Serify:用于 Twilio Verify 的轻量级 SMS 验证库

> 原文：<https://betterprogramming.pub/announcing-serify-a-lightweight-sms-validation-library-for-twilio-verify-8307a35699e0>

## 让发送和验证短信代码变得前所未有的简单

![](img/030efe8c43f2724ccb4bfa8bc1ed63a9.png)

系列化

[Serify](https://www.npmjs.com/package/serify) 是对[Twilio Verify](https://www.twilio.com/verify)[REST API](https://www.twilio.com/docs/verify/api)的包装。这个轻量级且简单明了的包装器为您提供了两种易于使用的发送和验证 SMS 代码的方法——并且它只有一个依赖项。这两种方法都利用了 async/await，使其易于与您现有的代码库集成。

# 例子

要使用发送验证码，请使用`start`方法，如下所示:

```
import Serify from 'serify';

const auth = new Serify({
    twilioServiceSid: 'YOUR_TWILIO_SERVICE_SID', // required
    twilioAccountSid: 'YOUR_TWILIO_ACCOUNT_SID', // required
    twilioAuthToken: 'YOUR_TWILIO_AUTH_TOKEN', // required
});

const start = async () => {
    try {
        const start = await auth.start({
            phone: 'USER_PHONE_NUMBER',
            country: 1,
        });

        console.log(start);
    } catch (error) {
        console.log(error);
    }
};

start();
```

要验证代码，请使用`verify`方法，如下所示:

```
import Serify from 'serify';

const auth = new Serify({
    twilioServiceSid: 'YOUR_TWILIO_SERVICE_SID', // required
    twilioAccountSid: 'YOUR_TWILIO_ACCOUNT_SID', // required
    twilioAuthToken: 'YOUR_TWILIO_AUTH_TOKEN', // required
});

const verify = async () => {
    try {
        const verify = await auth.verify({
            phone: 'USER_PHONE_NUMBER',
            country: 1,
            code: '1990',
        });

        console.log(verify);
    } catch (error) {
        console.log(error);
    }
};

verify();
```

# 获取令牌

Twilio 有时会令人困惑。除了一个**服务 SID** 之外，API 还需要一个**帐户级 SID** ，一个**帐户授权令牌**。所有的代币都可以在你的 [Twilio 控制台](https://www.twilio.com/console)中找到。

*   帐户级别 SID 和帐户身份验证令牌在您帐户的顶层提供。
*   在为 [Twilio Verify](https://www.twilio.com/verify) 产品创建应用程序时，可以找到特定于服务的 SID。

[https://www.npmjs.com/package/serify](https://www.npmjs.com/package/serify)