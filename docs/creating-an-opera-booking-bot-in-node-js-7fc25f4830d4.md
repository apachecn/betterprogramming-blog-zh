# 在 Node.js 中创建 Opera 预订机器人

> 原文：<https://betterprogramming.pub/creating-an-opera-booking-bot-in-node-js-7fc25f4830d4>

## 制作一个应用程序来获得一些很难买到的票

![](img/c827e408e5c62471145556d86efef177.png)

巴黎的奥普拉·卡尼尔——来自 Pixabay 的 [MustangJoe](https://pixabay.com/users/MustangJoe-2162920/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1248769)

我认为编程是一个人可以拥有的最方便的爱好。你不断地学习新的东西，构建你的思维来集中和组织你的代码，最重要的是:你得到了很少有人拥有的技能。

当我试图在 [*巴黎歌剧院*](https://www.operadeparis.fr/) 网站上获得试映票时，这个项目突然出现在我的脑海中。每次，大约 1800 张门票会在 1 到 2 分钟内全部售出。我心想，如果运气能让我订到最好的座位，我宁愿在机器人上工作，这是我应得的！

在编码的时候，我浏览了一下互联网，发现没有简单直接的教程教我如何制作这种脚本。我希望这能帮助人们避免糟糕设计的预订系统带来的烦恼，也许还能帮助开发者找到减少他们网站上不必要行为的方法(例如实现 *API 调用速率限制*)。

# 你需要什么

*   文本编辑器(*即 V* [*视觉工作室代码*](https://code.visualstudio.com/) *，* [*原子*](https://atom.io/) *…* )
*   node . js([如何安装 T29)](https://treehouse.github.io/installation-guides/mac/node-mac.html)
*   木偶师( [*怎么弄*](https://www.npmjs.com/package/puppeteer) )
*   您浏览器的检查器

# 第一步:侦察

在考虑任何编码之前，你需要了解你的目标网站是如何工作的。在*巴黎歌剧院，*像大多数订票网站一样，你需要两样东西:

*   登录方式(*访问预订表单*)
*   相关 API 请求(*更快地访问预订链接*)

## 登录

您可以采用“网站定向”的方法，直接找到用于登录的正确 API 请求。相反，我选择了更通用、更简单的方法:

1.  查找登录页面
2.  查找输入字段
3.  查找显示成功登录的特定标签

## 1.查找登录页面

那一个很容易。这是我为巴黎歌剧院找到的一张照片

## 2.查找登录输入字段

木偶师需要他们选择、键入和点击正确的字段。这就是为什么你需要给你的脚本提供正确的标签。为此，*右键单击用户名字段上的*并选择`Inspect element`。检查器将显示输入字段的标签。*在检查器的标签上右击*，悬停在`Copy`上，然后选择`Copy selector`。

完成后，将其粘贴到您的`*config.js*` *和*中，重复*密码输入*和*提交*按钮的过程。

## 3)找到特定于成功的标签

登录并复制仅在连接时出现*的选择器。大多数情况下，这将是一个*用户名*标签、*欢迎消息*或*注销按钮*。
你知道该怎么做:右键单击，复制，复制选择器并粘贴到`config.js,` 中，现在应该是这样的:*

```
module.exports = {
  LOGIN_PAGE: 'https://www.operadeparis.fr/login',
  USERNAME_SELECTOR: 'body > div.content-wrapper > div > div > div > div.LoginBox__form-container > form > input:nth-child(7)',
  PASSWORD_SELECTOR: 'body > div.content-wrapper > div > div > div > div.LoginBox__form-container > form > input:nth-child(8)',
  BUTTON_SELECTOR: 'body > div.content-wrapper > div > div > div > div.LoginBox__form-container > form > input.LoginBox__form-submit.Button.Button--black.Button--block',
  SUCCESS_SELECTOR_1: 'body > div.content-wrapper > div > section.personalarea__block--head.backToTop-visibilityTrigger',
  SUCCESS_SELECTOR_2: 'body > header > div > div > div.Header__actions > div.Header__account-only-mobile > div > a > span'
}
```

注意:你可能需要找到多个选择器，这取决于你的目标网站。例如，*巴黎歌剧院*有不同的桌面和移动版本，因此有两个不同的成功选择器。

# 拦截 API 调用

问题: [operadeparis.fr](https://www.operadeparis.fr) 从门票开售前五分钟开始就极其缓慢，使得链接在关键时刻几乎无法访问。
解决方案:进行 API 调用，因此我们只需要等待 JSON 响应，而不是等待所有其他文件(CSS、JS 等)加载。

这一步还要求您使用您的检查器，只是这次我们将选择标签为*网络*的选项卡，而不是*元素*(默认选择)。

在我们的例子中，找到正确的 API 请求非常简单。当加载性能页面时，您首先会看到许多请求。通过选择 *XHR* 将其过滤掉。您会发现一个标记为`performance`的请求。前途无量吧？选择它来发现期待已久的请求:
`https://www.operadeparis.fr/saison-19-20/ballet/hiroshi-sugimoto-william-forsythe**/performances**`

我们现在知道，要获得任何一部戏的信息，我们只需要在“公开”页面的末尾添加`/performances`。

## 我的预订链接在哪里？

要找到它，只需找到至少发布了一个预订链接的演出。
这是你在*巴黎歌剧院*得到的一个样本。

```
{
  "items": [
    {
      "dailyId": 1,
      "perfId": 2914,
      "content": {
        "performance": {
          "day": "jeu.",
          "dayNumber": "26",
          "month": "sept.",
          "time": "19:30",
          "type": "",
          "pictos": [],
          "mentions": [
            "Avant-première jeunes (-28 ans)"
          ]
        },
        "prices": "Voir les tarifs",
        "expand": {
          "is_gala": false,
          "is_full": false,
          "categories": [
            {
              "color": "FF9090",
              "code": "CatU",
              "price": "10 &euro;",
              "availability": false
            }
          ]
        },
        "open_to_sale_at": "En vente le 12 sept. à 12h00"
      },
      "template": "opening_notice"
    },
    {
      "dailyId": 2,
      "perfId": 2661,
      "content": {
        "performance": {
          "day": "ven.",
          "dayNumber": "27",
          "month": "sept.",
          "time": "19:30",
          "type": "",
          "pictos": [],
          "mentions": [
            "Première"
          ]
        },
        "prices": "de 135 à 210 &euro;",
        "expand": {
          "is_gala": false,
          "is_full": false,
          "categories": [
            {
              "color": "BCAA61",
              "code": "Optima",
              "price": "210 &euro;",
              "availability": true
            },
            {
              "color": "E46567",
              "code": "Cat1",
              "price": "190 &euro;",
              "availability": true
            }
          ]
        },
        "block": {
          "buttons": [
            {
              "type": "CTASubscribe",
              "url": "https://www.operadeparis.fr/billetterie/487-les-indes-galantes/abonnements",
              "text": "S’abonner",
              "is_bottom": false
            },
            {
              "type": "CTABook",
              "url": "https://billetterie.operadeparis.fr/api/1/redirect/product/performance?id=561186010&lang=fr",
              "text": "Réserver"
            }
          ]
        }
      },
      "template": "available"
    }
  ]
}
```

这里有一个可以找到预订链接的例子:
`Object.items[1].content.bloc.buttons[1].url`

我们想买先锋派活动的门票。由于这将始终是所有游戏中的第一个游戏，我们知道目标预订链接将始终列在:`Object.items**[0]**.content.bloc.buttons[1].url`

当你在开发一个预订机器人时，提前思考和处理 API 变化是很重要的。你应该准备好面对开场时的任何变化，防止你的剧本崩溃。因此，明智的做法是进行一些验证:

```
if (Object.items[0].content.bloc.buttons[1])
    // cool, this link exists
else if (Object.items[0].content.bloc.buttons[0])
    // one button has be disabled
else
    console.log(`final link couldn't be found in API response`) // handle unexpected change
```

# 第二步:计划

在进入编码部分之前，我们应该对我们的机器人将如何工作有一个总体的想法。

## 启动器

*   *获取性能名称*:做出正确的 API 请求
*   *获取用户凭证*:能够订票

## 主菜

*   *登录*:这应该是无缝预订流程的第一步
*   提出 API 请求
*   *刷新*:直到预订链接发布

## 甜点

*   重定向到预订页面
*   *通知用户*:在*巴黎歌剧院*上，一旦你第一次点击——或者模仿点击——链接，你就必须输入验证码才能进入等待队列。有了通知，用户不会错过也不会忘记输入。

# 第三步:编码

这是我最喜欢的部分。

## 启动器

让我们先从网站上解析所有的*前卫*表演开始:

```
const config = require('../config.js')
const getJSON = require('get-json')
const striptags = require('striptags')
const inquirer = require('inquirer')

// Get performance name + corresponding API link
async function parseOnePerformance(elem) {
  response = await getJSON(`${elem.link}/performances`, (error, response) => {
    if (error) {
      throw new Error(`Error while parsing ${elem.link}/performances`)
    }
    return response
  })
  if (response && response.items[0] && response.items[0].content.performance.mentions[0] == 'Avant-première jeunes (-28 ans)') {
    let title = striptags(elem.title).replace('&#8203;', '') // Clean performance name
    return [title, elem.link + '/performances']
  }
  return null
}

// Get performances from single venue
async function getPerformancesFromVenue(venueRequestPage) {
  response = await getJSON(venueRequestPage, async function(error, response){
    if (error) {
      throw new Error(`Error while parsing ${venueRequestPage}`)
    }
    return response
  })
  let events = {}
  for (let elem of response.datas) {
    result = await parseOnePerformance(elem)
    if (result && result[0] && result[1]) {
      events[result[0]] = result[1]
    }
  }
  return events
}

// Get performances for both venues (Opéra Garnier + Opéra Bastille)
module.exports.getPerformances = async function() {
  let events1 = await getPerformancesFromVenue(config.PERF_LIST_PAGE_GARNIER)
  let events2 = await getPerformancesFromVenue(config.PERF_LIST_PAGE_BASTILLE)
  return Object.assign(events1, events2)
}

// Display options to user
module.exports.getLink = async function(performances) {
  return new Promise((resolve) => {
    inquirer
      .prompt([
        {
          type: 'list',
          name: 'performance',
          message: 'Which performance do you want ?',
          choices: Object.keys(performances)
        }
      ])
      .then(answers => {
        process.env.OPERA_PERF_LINK = performances[answers.performance]
        resolve()
      })
    })
}
```

在这个文件中，我们进行两个主要步骤:

1.  *解析并从网站获取*所有想要的表演(我们不想要那些不属于 *Avant-Première* 类型的)。
2.  选择步骤，用户可以选择他们想要的。

因为 API 不会一次返回所有的表演，所以我选择分别调用来自 *Opéra Garnier* 和 *Opéra Bastille* 的表演。

一旦解析完成，我们剩下的字典格式为:
`{ performanceName: performanceApiLink, ... }`。当用户选择一个性能时，我们将立即知道要调用哪个 API。

现在，对于登录，我们可以使用`env`变量。我们需要两个函数:

1.  询问用户是否希望*使用保存的凭证*(以防他之前导出了凭证)
2.  *询问凭证*(如果他决定手动输入)

```
const inquirer = require('inquirer')

// Ask if user wants to use saved credentials
module.exports.keepCredentials = async function() {
  return new Promise((resolve) => {
    inquirer
      .prompt([
        {
          type: 'confirm',
          name: 'credentials',
          message: 'Do you want to use your saved credentials ?',
          default: [ true ]     
        }
      ])
      .then(answers => {
        resolve(answers.credentials)
      })
    })
}

// Ask for users credentials and store them as env variables
module.exports.getCredentials = async function() {
  return new Promise((resolve) => {
    inquirer
      .prompt([
        {
          type: 'input',
          name: 'username',
          message: 'Username ?'    
        },
        {
          type: 'password',
          name: 'password',
          message: 'Password ?'
        }
      ])
      .then(answers => {
        process.env.OPERA_USERNAME = answers.username
        process.env.OPERA_PASSWORD = answers.password
        resolve()
      })
    })
}
```

还有一件事—我们需要编写我们的登录函数:

```
const config = require('../config.js')

module.exports.login = async function(page) {

  // Input credentials
  await page.goto(config.LOGIN_PAGE);
  await page.click(config.USERNAME_SELECTOR,{ clickCount: 3 })
  await page.keyboard.type(process.env.OPERA_USERNAME)
  await page.click(config.PASSWORD_SELECTOR,{ clickCount: 3})
  await page.keyboard.type(process.env.OPERA_PASSWORD)
  await page.click(config.BUTTON_SELECTOR)

  // Check if login was successful
  try {
    const response = await page.waitForNavigation({waituntil: 'loaded'});
    await response.request().redirectChain();
    await page.waitForSelector(`${config.SUCCESS_SELECTOR_1}, ${config.SUCCESS_SELECTOR_2}`)
    return
  }
  catch (error) {
    throw new Error('Failed to log in, try again')
  }
}
```

太好了，我们成功了！我们有了启动程序所需的所有主要函数。想看看这一切是怎么加起来的吗？我们走吧。

## 主菜

还记得我们的第一个函数吗？`parseEvents.js`和`getInputs.js`？
让我们在文件的开头使用它们`getMeATciket.js`:

```
// Get avant-premiere links
try {
  console.log('Getting avant-premières of 19-20 season...')
  performances = await events.getPerformances()
  await events.getLink(performances)
} catch (error) {
  console.log(error)
  console.log('Error while parsing performance pages')
  process.exit(1)
}

// Get credentials
try {
  if (!process.env.OPERA_USERNAME || !process.env.OPERA_PASSWORD) {
    console.log(`Save your credentials with "export OPERA_USERNAME=yourUsername && export OPERA_PASSWORD=yourPassword"`)
    await inputs.getCredentials()
  } else {
    response = await inputs.keepCredentials()
    if (response == false) {
      await inputs.getCredentials()
    }
  }
} catch (error) {
  console.log('Error while getting your credentials')
  process.exit(1)
}
```

你必须在每一步都捕捉潜在的错误，这样你就知道在崩溃的情况下应该如何修复。

那很容易。现在让我们看看脚本的核心逻辑是如何工作的:

```
// Launch puppeteer
let browser
try {
  browser = await puppeteer.launch({
    headless: false,
    defaultViewport: null
  })
} catch (error) {
  console.log('Error while getting performance link')
  process.exit(1)
}

const page = await browser.newPage()  
await page.setDefaultTimeout(0) // Unset timeout -> when API slows down, script won't crash

// Login to users account
try {
  await login.login(page)
} catch (error) {
  console.log('Login failed (timeout or wrong credentials)')
  process.exit(1)
}
await page.goto(process.env.OPERA_PERF_LINK, { waitUntil: 'load' })

await page.content()
body = await page.evaluate(() => { // Getting page content
  return JSON.parse(document.querySelector('body').innerText) // Retrieve API response
})

// Repeat until booking available
while (body.items[0].template !== 'available') {
  await page.goto(process.env.OPERA_PERF_LINK, { waitUntil: 'load' })
  body = await page.evaluate(() => {
    return JSON.parse(document.querySelector('body').innerText)
  })
}
```

我们首先创建一个浏览器实例。我们将把它设置为 *false* 来看看它是如何工作的。`DefaultViewport`被设置为 *null* ，这样新页面就不会被限制在一定的宽度和高度。

在这一部分，我们还:
1。登录
2。进行 API 调用(*对应用户选择* )
3。从 JSON 响应
4 中检索数据。刷新直到目标事件未被标记为“*可用*

您还可以添加一个刷新率指示器来监控脚本的活动。

## 甜点

脚本的结尾比其余部分容易。你只需要将用户重定向到找到的链接，并确保他看到它，无论他当时在做什么。

```
await utils.beep()
console.log(body.items[0].content.block.buttons[1].url)
console.log('Found it ! Getting you there...')

await page.bringToFront()

await page.goto(body.items[0].content.block.buttons[1].url, { waitUntil: 'networkidle0' })
return 0
```

我首先使用了一个名为 [play-sound](https://www.npmjs.com/package/play-sound) 的 NodeJS 包，在发现链接时播放声音。一旦声音被播放，它在用户终端显示一个链接，用户被重定向到目标页面。`bringToFront()`最后关注相应的浏览器页面。

就是这样！你刚刚设计了一个工作的预订机器人。正如你所看到的，它不需要太多的编码技能。虽然这个程序适用于特定的网站，但请注意，它可能会采用不同的方法为另一个预订系统做同样的事情。

请注意，本程序无意以任何方式对造成任何伤害。它还没有强大到足以导致任何拒绝服务。也不意味着每个想买票的人都可以用它。

别傻了，明智地使用它。

以下是主回购的链接:

[](https://github.com/fabrahaingo/cute_little_bot) [## 可爱的小机器人

### 打开您的终端应用程序。它的图标看起来像这样:下载这个存储库:git clone…

github.com](https://github.com/fabrahaingo/cute_little_bot)