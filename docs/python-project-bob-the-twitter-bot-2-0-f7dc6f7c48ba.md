# Python 项目——Bob the Twitter Bot 2.0

> 原文：<https://betterprogramming.pub/python-project-bob-the-twitter-bot-2-0-f7dc6f7c48ba>

给我的 Twitter 机器人升级，帮助我找到免费的机器学习资源

![](img/155acc74396020e7fbe74ce7eca3f7b6.png)

作为我的第一个 Python 项目之一，我创建了 Bob，一个在我的 Twitter 上发布励志名言的 Ywitter 机器人。

鲍勃每天早上 8 点都会引用 theysaidso.com 的话，给我推特上的每个人以灵感。

在过去的几个月里，我一直在自学机器学习和数据科学，努力成为一名数据科学家。旅程非常有趣，我非常喜欢。

然而，由于我一直在处理不同的数据科学项目，我意识到我需要学习更多关于 Pythonic 代码的知识，以便有效地遍历和操作数据集。因此，在接下来的一个月里，我将阅读《流畅的 Python》，同时阅读一本关于统计学的书，为自己进入机器学习算法的高速运转做准备。

项目对于学习来说很重要，我决定我的机器人应该升级了，因为我在 7 月份就完成了。

回头看看代码，我很震惊——到处都是创建的函数。现在我对 Python 面向对象的本质有了更多的了解，我认为这是一个很好的机会来重构我的机器人，并使用它来使我的生活变得更容易。互联网巨大，免费资源那么多。我觉得用 Bob 在 Twitter 上搜索免费的机器学习资源，然后把链接发给我会是一个有趣的想法。

Bob 1.0 和 2.0 的完整代码可以在我的 [GitHub 库](https://github.com/Kristian-Roopnarine/Bob-the-Twitter-bot)中找到:

[](https://github.com/Kristian-Roopnarine/Bob-the-Twitter-bot) [## 克里斯蒂安-鲁普那林/鲍勃-推特-机器人

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.co](https://github.com/Kristian-Roopnarine/Bob-the-Twitter-bot) 

# 鲍勃是如何升级的？

一般来说，创建类有助于保持代码的整洁和可读性，因为它有助于关注点的分离。

您可以创建一个类来存储数据，然后创建方法来处理这些数据，这样就可以在一个地方找到所有内容。

为了重构这段代码，我认为创建一个`‘TwitterAccount’`类是个好主意，所有与 Twitter 账户相关的方法都将放在这个类中。

对于这个特定的机器人，我需要一个方法来通过 Tweepy 访问我的 Twitter 帐户，发布鼓舞人心的报价，然后创建一个新的方法来搜索关于免费机器学习资源的推文。我们需要一种初始化该类的方法和一种访问 Twitter 帐户的方法。

```
class TwitterAccount:    

def__init__(self,consumer_key,consumer_secret,access_token,access_token_secret):        
    self.consumer_key = consumer_key        
    self.consumer_secret= consumer_secret        
    self.access_token =access_token        
    self.access_token_secret = access_token_secret        
def access(self):        
    auth =       tweepy.OAuthHandler(self.consumer_key,self.consumer_secret)             auth.set_access_token(self.access_token,self.access_token_secret)            return tweepy.API(auth) # returns api object
```

在这里，我用访问帐户所需的信息创建了一个`TwitterAccount`类:访问和消费者密钥。

access 方法返回一个 API 对象，该对象用于通过使用这些键与 Twitter 帐户进行通信。

现在，我们需要更多的方法来发布报价，然后通过推文进行搜索。发布报价非常简单，因为它只是我们之前为 Bob 1.0 编写的代码的扩展:

```
def post(quote_details):
      self.access().update_status('"%s." \n-%s \n\n Daily   Inpsiration From Bob the Twitter Bot' % (quote_details))
```

和以前一样，报价细节将是一个向 theysaidso.com 发出当天报价请求的函数，并从相应的 JSON 中提取报价信息。

```
def get_quote(url):    
    response = requests.get(url)    
    quote_info =   json.loads(response.content).get('contents').get('quotes')[0]                                         quote = quote_info.get('quote')    
    author = quote_info.get('author')    
    return quote,author
```

上面的函数将返回我们将输入到`TwitterAccount.post()`方法中的`quote_details`。现在我们要做的就是找出如何在 Twitter 上搜索特定的推文，然后在一天中的特定时间把这些推文发给自己。

# 使用 Python 发送文本消息

我认为下一个重要的部分是弄清楚如何使用 Python 来发送文本消息。弄清楚这一点后，我可以阅读关于使用 Tweepy 搜索 Twitter 的文档。

我去 YouTube 上找了一些如何使用 Python 发送短信的视频，亚马逊网络服务有一个简单的通知服务(SNS)，你可以在那里发送短信。

您创建一个帐户(您可以获得长达 12 个月的免费服务)，并为 SNS 注册一个用户。

然后安装 AWS CLI 来设置您的凭证，当您运行脚本时，程序将能够使用这些凭证发送消息。

我们用来发送这些消息的模块是 Boto 3，所以你需要从命令行安装它。设置好凭证后，导入 Boto 3。发送消息的代码只有两行。

```
import boto3client = boto3.client('sns',region_name='us-east-1')client.publish(PhoneNumber='', Message='')
```

您可以根据发送消息的目的地选择一个区域，并将其存储在客户端变量中。然后，您只需应用 publish 方法向相应的电话号码发送 SMS 文本。既然我们已经解决了这个问题，让我们看看如何使用 Tweepy 搜索 Twitter。

# 使用 Tweepy 搜索推文

Tweepy 有很多方法可以让你和你的 Twitter 账户互动。有一些允许你关注用户，检查你的时间表，搜索标签，等等。

在编写代码之前，我在 Twitter 网站上做了一些测试，只是为了了解搜索各种主题时的输出。

我注意到，当我搜索像“机器学习”或“数据科学”这样的词时，我会得到所有包含这些词的推文(当然)。然后我试着搜索标签，结果更符合我的要求。主要是专业人士发布关于趋势和资源的信息。

我们已经有了搜索项，所以让我们来看看如何使用 Tweepy 进行搜索。`tweepy.Cursor`通常用于使用不同的搜索查询来搜索 Twitter。它将返回一个 cursor 对象，我们只需从这些对象中提取文本来获取我们的 tweets。

因为这是我们在 Twitter 账户上要做的事情，所以把它作为一个方法添加到我们的`TwitterAccount`类中是合适的。

```
def search_hashtags(self,hashtags): #returns a cursor object tweets =   tweepy.Cursor(self.access().search,q=hashtags,result_type='recent'). items(100) tweet_info = [tweet.text for tweet in tweets] return tweet_info
```

我们的`tweepy.Cursor`有几个参数——第一个是我们希望机器人做的动作，即搜索 Twitter。

`q` 参数是搜索项，我们可以通过最近的 100 篇文章过滤搜索。

然后，我们执行列表理解，从光标对象中的每条 tweets 中提取文本。

现在，我不想要所有这 100 条推文，所以我将根据一些关键词过滤这些推文，这些关键词将帮助我找到免费的机器学习资源。为此，我认为我们应该给`TwitterAccount`类添加另一个方法。

```
def filter_feed(self): search_word = '#MachineLearning -filter:retweets' filter_words = ['free','course','book','eBook'] cs_tweets = self.search_hashtags(search_word) text_tweets =[] for filters in filter_words: for tweets in cs_tweets: if filters in tweets: text_tweets.append(tweets)return text_tweets[:5]
```

我们有自己的搜索词，即#MachineLearning，我们将过滤掉所有的转发，这样我们就不会返回重复的帖子。我主要想搜索免费的资源，要么是一本书，要么是一些课程。

我们将使用嵌套的 for 循环来循环每个 tweet 上的每个过滤词，并只返回包含这些过滤词的 tweet。我肯定有更有效的方法来检查推文中的这些过滤词，但这是我目前想到的解决方案。鲍勃肯定会再次升级！

然后我们最多返回五条推文，我们将使用这个列表来发送短信。

# Bob 2.0 的收尾工作！

我们已经有了升级 Bob 所需的一切，现在我们只需要把拼图的碎片补上。首先，我们需要稍微改变一下之前的算法，算出发送这些短信的时间。

新算法看起来像:

```
TwitterAccount = TwitterAccount(consumer_key,consumer_secret,access_token,access_token_secret)while True: t = check_time() if t == '08:00 AM' or t == '08:01 AM': print(t,flush=True) TwitterAccount.post(get_quote(url)) time.sleep(61) elif t == '17:00 PM' or t =='17:01 PM': text_tweets = TwitterAccount.filter_feed() for tweets in text_tweets: client = boto3.client('sns',region_name='us-east-1') client.publish(PhoneNumber='', Message=tweets) time.sleep(61) else: t = check_time() print(t,flush=True) print('It\'s not time yet',flush=True) time.sleep(50)
```

首先，我们初始化一个 Twitter 帐户类，并创建一个无限循环，因为这个程序将永远运行。

它会每隔 50 秒检查一次时间，如果是早上 8 点，它会在我的 Twitter 上发布一段引文。如果是下午 5 点，它会搜索 Twitter，把免费资源发给我。

# 后续步骤

下个月，我将带着这个去真正地学习机器学习和数据科学的统计原理。

我的主要精力将放在改进我的 Python 上，我相信这反过来将有助于创建高效的代码来执行数据分析。我仍然会做我的机器学习项目，但现在速度会慢一些。

请继续关注我们的下一个 Python 项目。