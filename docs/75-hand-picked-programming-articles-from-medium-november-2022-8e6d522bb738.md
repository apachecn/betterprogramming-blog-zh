# 来自 Medium 的 75 篇以上精选编程文章—2022 年 11 月

> 原文：<https://betterprogramming.pub/75-hand-picked-programming-articles-from-medium-november-2022-8e6d522bb738>

嘿大家好，

这是咖啡字节，由更好的编程通讯涵盖我们最喜爱的故事跨越媒介，以帮助您发现伟大的内容和新的声音。上个月，在中等代码块中添加了[原生语法高亮！你知道现在代码块中有一个 Diff 选项吗？对读者，对作者，对出版商都是双赢！](https://blog.medium.com/code-blocks-with-syntax-highlighting-53343df53c4f)

在过去的几天里，我在一个编译器编写平台上工作，偶然发现了由沃尔特·舒尔茨[撰写的](https://medium.com/u/14ea84373f1f?source=post_page-----8e6d522bb738--------------------------------)[正则表达式教程](https://medium.com/better-programming/how-to-take-the-derivative-of-a-regular-expression-explained-2e7cea15028d)——一个使用吃豆人解释的交互式指南。

本期内容:最新的 OpenAI 和 Stable Diffusion 新增内容、迁移乳齿象实例、Rust 不断增长的使用量、探索编程语言概念和工具，以及大量与工程相关的建议。所以，让我们开始吧！

如果你最近在 Twitter 上很活跃，你可能知道乳齿象是什么。 [Kris Nóva](https://medium.com/u/779e99eb0b8e?source=post_page-----8e6d522bb738--------------------------------) 在将她的`hachyderm.io`乳齿象实例迁移到临时环境时，分享了一个[学习和发现的故事](https://medium.com/@kris-nova/experimenting-with-federation-and-migrating-accounts-eae61a688c3c)。为了开始使用 Python 中的[乳齿象 API，](https://medium.com/@martin.heinz/getting-started-with-mastodon-api-in-python-9f105309ed43) [Martin Heinz](https://medium.com/u/4cbd6b36e62a?source=post_page-----8e6d522bb738--------------------------------) 为我们提供了一个速成班。

我不知道 Twitter 的发展方向，但是 Community Notes 算法很有意思。Maximilian Strauss 在这里向我们展示了[算法以及 Twitter 在未来将如何呈现信息](/how-algorithms-in-elon-musks-twitter-will-determine-truth-c1a025caa45a)。

对算法着迷？Clive Thompson 收集了[改变世界的五个小代码片段](/tiny-snippets-of-code-that-changed-the-world-fda104afc0d0)。一个关于页面排名算法的问题引起了我的注意——考虑到谷歌的 [SEO 垃圾信息](https://mdubakov.medium.com/google-please-do-something-with-your-ads-and-seo-spam-99a6b039354c)，这个问题就更加突出了。

关于谷歌搜索这个话题，这里有一些指南可以帮助你建立一个搜索引擎:

*   [如何在 Rust](https://sachaarbonel.medium.com/how-to-build-a-semantic-search-engine-in-rust-e96e6378cfd9) 中构建语义搜索引擎 [Sacha Arbonel](https://medium.com/u/2c0d6b5ff54a?source=post_page-----8e6d522bb738--------------------------------)
*   [使用 HuggingFace 和 Elasticsearch 的语义搜索](/implementing-nearest-neighbour-search-with-elasticsearch-c59a8d33dd9d)由 [Odunayo Ogundepo](https://medium.com/u/6ca969333607?source=post_page-----8e6d522bb738--------------------------------)
*   [创建自然语言语义搜索来查找 gif](/create-a-natural-language-semantic-search-to-find-gifs-81f9be79850e)作者[詹姆斯·布里格斯](https://medium.com/u/b9d77a4ca1d1?source=post_page-----8e6d522bb738--------------------------------)

# OpenAI 中的新功能

OpenAI 的对话聊天机器人 ChatGPT 仅在几天前发布，但已经在互联网上掀起了风暴。从写博客到解决编码问题，再到在机器人内部构建虚拟机，可能性是无穷无尽的。革命性的人工智能可以同时让软件工程师兴奋和害怕。克里斯·托米奇回答了存在主义问题——“我会被需要吗？”

转到另一个相关的问题——谷歌搜索完成了吗？这很难预测，但像使用 OpenAI 的耳语工具(再次感谢 [James](https://jamescalam.medium.com/) )的 [YouTube 搜索修复这样的项目，可能是搜索引擎巨头的一个担忧。](/fixing-youtube-search-with-openais-whisper-90bb569073cf)

我要求 ChatGPT 构建一个 SwiftUI 应用程序，尽管结果对于琐碎的任务来说很棒，但有时它毫无意义——就像机器人声称 [SwiftUI 不包含原生视频播放器](https://imgur.com/a/eIjvh8C)——而它显然包含。或许，LLM 是在 Apple Docs 上接受培训的——苹果没有在长格式指南上投入足够的资源终于说得通了。

![](img/50c6376a902e39f7b3bf14c69fd3ac6c.png)

图片由詹姆斯·布里格斯提供。

感觉 DALL-E 的 API 已经推出很久了。然而，这仅仅是几周前的事。由 Bill Cava[编写的](https://medium.com/u/a0fa37c8ac38?source=post_page-----8e6d522bb738--------------------------------)[数据可视化演示](/using-dall-e-for-data-visualization-59df580e66ab)和由 yours truly 编写的 Swift 中的[实现来裁剪和替换人脸应该可以让您入门。](/dall-e-api-in-swift-generate-faces-e39a2625a5a2)

为了 GPT-3 相关的灵感，[库纳尔·德赛](https://medium.com/u/5581405b34d?source=post_page-----8e6d522bb738--------------------------------) [建造了一个不和谐支持机器人](https://medium.com/@kunaljaydesai/building-a-gpt-3-powered-discord-support-bot-8791b2d2681d)，[保罗·泰勒](https://medium.com/u/812183860d79?source=post_page-----8e6d522bb738--------------------------------)使用微调过的 API 创建了一个[垃圾邮件过滤器。](/build-a-spam-checker-with-openai-and-gpt-3-8d12188780a2)

从 OpenAI 开始，稳定扩散 2.0 现已推出。这里有三个相关的指南可以帮助你迅速进入状态:

*   [关于扩散模型的扩散？](https://nikux.medium.com/diffused-about-diffusion-models-e36c26ac1442)由 [Nikhil Rasiwasia](https://medium.com/u/48270cb5880f?source=post_page-----8e6d522bb738--------------------------------)
*   [如何在亚马逊 SageMaker 上部署稳定的扩散模型](/meet-the-open-source-ai-that-is-transforming-video-editing-gaming-art-music-and-more-583dc19cfbf6)作者[贾斯汀·穆勒](https://medium.com/u/1930c4fc0a74?source=post_page-----8e6d522bb738--------------------------------)
*   [马克·罗德塞斯](https://mark-rodseth.medium.com/building-a-giphy-like-stable-diffusion-slackbot-62ac50b8a878)建造一个类似 Giphy 的稳定扩散 Slackbot

除此之外，苹果发布了[在苹果芯片上支持核心 ML 的稳定扩散](https://machinelearning.apple.com/research/stable-diffusion-coreml-apple-silicon)。应用开发者的好日子就在前面！

对于 Spring 开发者来说，大量的项目发布发生了，在这个 [Twitter 帖子](https://twitter.com/maciejwalkowiak/status/1595883981374996486)中总结得很好。[文琪·格兰茨](https://medium.com/u/ce7cd5b8b74a?source=post_page-----8e6d522bb738--------------------------------)将通过一本关于 [GraalVM 原生映像支持](/going-native-with-spring-boot-3-ga-4e8d91ab21d3)的实用指南，带我们了解《Spring Boot 3 GA》中的新内容。

11 月 27 日见证了 Heroku 免费等级的贬值。如果您正在寻找替代方案，[Jerry Ng](/say-goodbye-to-heroku-free-tier-here-are-the-4-alternatives-8d82bb10b495)[的](https://medium.com/u/8606bf5a73f5?source=post_page-----8e6d522bb738--------------------------------)这里有一个有用的列表……以下指南应该有助于您快速掌握这些工具:

*   [以下是如何将 Web 应用部署到 AWS EC2](/migrate-from-heroku-to-aws-ec2-756328d8e58a) 的方法
*   [Antoine doerane](https://antoine-doeraene.medium.com/deploying-a-full-stack-scala-application-on-fly-io-f80ca9de9b13)在 fly.io 上部署全栈 Scala 应用
*   [将 Node.js 应用从 Heroku](/migrating-a-node-js-app-to-cloudflare-workers-from-heroku-62c679552af) 迁移到 cloud flare Workers[Patrick Chiu](https://medium.com/u/8d55e74d4410?source=post_page-----8e6d522bb738--------------------------------)
*   [Luis Soares](https://medium.com/codex/deploying-a-kotlin-app-in-railway-a-slack-bot-f1d7a2386652)[在铁路上部署一个 Kotlin 应用程序](https://medium.com/u/54ea764ff544?source=post_page-----8e6d522bb738--------------------------------)
*   [如何在 Render.com 部署一个纯 API 的 Ruby on Rails 应用](https://medium.com/@indigodavid/how-to-deploy-an-api-only-ruby-on-rails-application-to-render-6012a19ba2cd)

# 语言概念

## JavaScript 等

*   [在 React 中获取数据:失去承诺的案例](https://adevnadia.medium.com/fetching-data-in-react-the-case-of-lost-promises-17319b7072d6) — [Nadia Makarevich](https://medium.com/u/bcdf0570e41c?source=post_page-----8e6d522bb738--------------------------------) 带我们深入了解 React 中通过承诺和竞争条件获取数据的基本原理。
*   [如何在 TypeScript 中编写类型安全的代码](/type-safe-typescript-with-type-narrowing-649450d708df) — [Rainer Hahnekamp](https://medium.com/u/4212e7684dc0?source=post_page-----8e6d522bb738--------------------------------) 为在类型断言上使用类型收缩提供了一个很好的例子，并描述了处理联合类型的各种技术
*   [揭开“flushPromises”的神秘面纱](/de-mystifying-flushpromises-b13bc2254f95) — [Suraj Pillai](https://medium.com/u/9fa5de79e9c6?source=post_page-----8e6d522bb738--------------------------------) 通过在本实践教程中研究`flushPromises`和`Promise.resolve`之间的差异，帮助我们加深对 JavaScript 非阻塞架构的理解。

## 计算机编程语言

*   [渴望 vs 严格 vs 不严格 vs 懒惰——这一切意味着什么？](/a-nibble-of-lazy-evaluation-22a93e05df3e)**—**[Kurt Schelfthout](https://medium.com/u/9ce8ae5e2cb4?source=post_page-----8e6d522bb738--------------------------------)向我们简要介绍了各种评估策略以及为什么懒惰评估很重要。Haskellers 对懒惰的评价引以为豪！你知道 Python3 里的地图很懒吗？
*   [超越 Python 的 AssertionError](/how-to-overwrite-asserterror-in-python-and-use-custom-exceptions-c0b252989977) — [马尔钦·科萨克](https://medium.com/u/4762f0cff9b2?source=post_page-----8e6d522bb738--------------------------------)通过实践指南，教我们如何重写`AssertionError`和使用自定义异常。

## 迅速发生的

*   [从循环到函子的旅程](https://marekgalik.medium.com/journey-from-loops-to-functors-606b6fc4e4c1) — [Marek Galik](https://medium.com/u/b211a4c733fe?source=post_page-----8e6d522bb738--------------------------------) 描述了 Swift 中的函子，这些函子在函数式编程中更受欢迎。
*   [Swift 作为终极领域语言](/the-ultimate-domain-language-declarative-swift-37b0a04e4e32) — [Manuel Meyer](https://medium.com/u/11f6da876891?source=post_page-----8e6d522bb738--------------------------------) 提出了一种新范式——声明性 Swift——一种高效且有效地创建领域驱动代码的风格。
*   [CodingKeys:超越名称匹配。帕特里西奥·伊格纳西奥·法里亚·巴尔迪维娅](https://medium.com/@Pfarinav/codingkeys-beyond-the-name-matching-1ff16b24fd86)

## 去

*   [使用 Go 处理大型文件](https://medium.com/@snassr/processing-large-files-in-go-golang-6ea87effbfe2) — [snassr](https://medium.com/u/92c439e19536?source=post_page-----8e6d522bb738--------------------------------) 探索了使用基准比较处理大型 CSV 文件的不同方法。在这篇文章中，我们可以使用一些有用的并发编程实践。
*   [利用 Golang 泛型的集合](https://medium.com/@noahschumacher/utilizing-sets-with-golang-generics-316b4fe2ca28)——如果你不知道泛型， [Noah Schumacher](https://medium.com/u/250e4a2449f8?source=post_page-----8e6d522bb738--------------------------------) 有一个方便的指南，通过实现集合来磨练你的技能。
*   [实现一个 Golang 泛型函数来处理任何 HTTP 请求](/golang-a-powerful-generic-function-to-make-any-http-request-b4d660834c10) — [Chris Frewin](https://medium.com/u/86eefd5342b8?source=post_page-----8e6d522bb738--------------------------------) 分享了如何通过探索 Go 中的泛型功能来构建该函数的完整演示。
*   [Go 中三元运算符的替代方案](/go-has-no-ternary-operators-here-are-some-equivalents-316debbc9caa)—[来自 Java 背景的赖](https://medium.com/u/cdb017d6b63a?source=post_page-----8e6d522bb738--------------------------------)讨论了缺少三元运算符的原因，并给出了几个替代方案。

# Rust 的采用增加了

锈病在最近一段时间呈上升趋势。谷歌[最近的报告强调了](https://9to5google.com/2022/12/01/android-memory-safety-rust/) Rust 占 Android 13 所有新本机代码的 21%以及内存安全问题的下降，而 [Adrian Taylor](https://medium.com/u/a168554c7e0b?source=post_page-----8e6d522bb738--------------------------------) 带我们了解了在 Chromium 浏览器中使用 Rust 包装的 C++ 在稳定性和性能方面的优势。

有一个宝库锈指南，以帮助你得到启发。例如， [Dotan Nahum](https://medium.com/u/333bfa9d9c3?source=post_page-----8e6d522bb738--------------------------------) 通过[构建桌面托盘启动器应用](/tauri-vs-electron-for-tray-apps-ed15974f35ce)、 [David Dal Busco](https://medium.com/u/94f0c8061324?source=post_page-----8e6d522bb738--------------------------------) 分享[如何将罐智能合约从 Motoko 迁移到 Rust](https://medium.com/dfinity/how-to-migrate-canister-smart-contracts-from-motoko-to-rust-3446a4b0c2ff) 、 [Evan Cameron](https://medium.com/u/38b8fb90416e?source=post_page-----8e6d522bb738--------------------------------) [重写基于 Java 的 DNS 解析器实现](https://medium.techatscale.com/open-heart-surgery-from-java-to-rust-88d042ad43e7)、 [Brook Jeynes](https://medium.com/u/e2e7b4b3e940?source=post_page-----8e6d522bb738--------------------------------) [构建博客应用](https://medium.com/@jeynesbrook/building-an-api-in-rust-with-rocket-rs-and-diesel-rs-clean-architecture-8f6092ee2606)。

在介绍了针对 Rust 的新编程语言 Letlang 之后， [David Delassus](https://medium.com/u/64e2b344388d?source=post_page-----8e6d522bb738--------------------------------) 回来给我们更多关于它的细节和未来的路线图。

尽管 Rust 对性能关键的工具很有用，马特·韦尔什认为它[并不最适合那些需要快速发布产品的初创公司](https://mdwdotla.medium.com/using-rust-at-a-startup-a-cautionary-tale-42ab823d9454)。

# 讨论

*   熊猫中的连锁方法:糟糕的形式还是成功的秘诀？——蟒蛇驯兽师[马特·哈里森](https://twitter.com/__mharrison__)的例子引起了不小的轰动。 [David Amos](https://medium.com/u/2b3100272a2a?source=post_page-----8e6d522bb738--------------------------------) 带我们采访了 Matt，讨论代码可读性、编写好的 Pandas 代码，以及在 Pandas 中使用方法链接是否可取。从这次谈话中有很多东西需要解开。
*   [什么时候无服务器比容器贵？](/when-is-serverless-more-expensive-than-containers-23b318662ce6) — [Allen Helton](https://medium.com/u/506242edfbaf?source=post_page-----8e6d522bb738--------------------------------) 对比由 Lambda functions 独家支持的无服务器应用程序，检查了针对计算优化的负载平衡 EC2 车队，以显示一定量的流量提示，从而使无服务器变得更加昂贵。
*   [Scala 中的花括号是怎么回事？](https://medium.com/@oliver_winks/what-is-going-on-with-curly-braces-in-scala-cf1dd98080b) —基于缩进的语法在 Pythonistas 中很流行，但是它适合 Scala 吗？奥利弗认为新的可选括号语法并不都是阳光和棒棒糖。

# 我们发现了一些很酷的项目

![](img/853fa4a489a0713dfd929448bff5923c.png)

*   [迈克尔·j·福特汉姆](https://medium.com/u/d4b2f465b56d?source=post_page-----8e6d522bb738--------------------------------)做得非常好[使用 Lua 为 Playdate 控制台](/how-to-build-a-game-for-the-playdate-console-using-the-playdate-sdk-and-lua-cc8fd2079433)构建了一个守门员游戏，并分解了游戏开发的不同领域。
*   Will Howard 有一个方便的指南来帮助使用 Three.js 构建 web GL Dot Spheres——就像我们在 Stripe 和 GitHub 主页上看到的那些。
*   [Sergio Sánchez](https://medium.com/u/635b5cbb07d1?source=post_page-----8e6d522bb738--------------------------------) 解释了一种针对无密码解决方案的[架构方法](/a-passwordless-authentication-architecture-based-on-a-one-time-code-approach-2ef30d0f25d8)，该解决方案由使用 Kotlin 的轻量级云原生微服务实施。
*   [Mattia Maldini](https://medium.com/u/faccadc6c9f2?source=post_page-----8e6d522bb738--------------------------------) 展示了[如何用 LVGL 和 SDL2 作为后端构建一个 C GUI 应用](/build-a-c-gui-application-with-lvgl-and-a-sdl2-as-the-backend-85a07ee94a9f)
*   [Piotr Jaworski](https://medium.com/u/3aafa1d2ca03?source=post_page-----8e6d522bb738--------------------------------) [使用 gamma cv](https://piotrjaworski.medium.com/gammacv-a-simple-custom-operation-example-b502d9365b0e)——一个 JavaScript 计算机视觉库，编写了一个简单的自定义操作示例。
*   [Daniele Fontani](https://medium.com/u/97d01fcd4684?source=post_page-----8e6d522bb738--------------------------------) 创建 Bashy — [一个使用 Go](/bash-argument-parsing-e4bf28b39956) 打包 Bash 脚本的开源工具。
*   [点云可视化用 Three.js](https://medium.com/@adamias/point-clouds-visualization-with-three-js-5ef2a5e24587) 作者[亚当塞尔尼](https://medium.com/u/1b304f065003?source=post_page-----8e6d522bb738--------------------------------)——你知道如何达到完美吗？只需结合中途，稳定扩散，深度图，面部变形，和一点三维。
*   [一个小图形，以及在此之前的 36 个小时](/a-baby-graphiqal-and-the-36-hours-that-preceded-it-8a574f5cac73)——[伊莎贝尔·伊利亚](https://medium.com/u/6ddaa089ec94?source=post_page-----8e6d522bb738--------------------------------)分享了她和她的团队使用 Electron.js 构建桌面应用程序的过程。黑客马拉松很有趣！
*   [Peter van Vliet](https://medium.com/u/ec9f0e30558b?source=post_page-----8e6d522bb738--------------------------------) 分享他在[的旅程，通过不使用 Jitar 构建 API](/how-i-speed-up-full-stack-development-by-not-building-apis-7f768335bec6)来加速全栈开发过程。
*   [布莱恩·梅奥](https://medium.com/@protiumx)将他的[个人网站变成了终端](https://medium.com/@protiumx/an-interpreted-language-you-can-try-in-my-terminal-website-bf05022594fe)。

# 实践指南

## Web 开发

*   [Angular v15 现已上市！](https://blog.angular.io/angular-v15-is-now-available-df7be7f2f4c8)作者[明科·格切夫](https://medium.com/u/a7db01a91fcb?source=post_page-----8e6d522bb738--------------------------------)
*   [我们如何针对 web 性能优化我们的 Next.js 应用](https://medium.com/typeforms-engineering-blog/how-we-optimized-our-next-js-site-for-web-performance-88bed643c85c)Artur Szott
*   [如何让 Next.js 13 的优化字体与顺风 CSS](https://levelup.gitconnected.com/how-to-make-next-js-13s-optimized-fonts-work-with-tailwind-css-c3c5e57d38aa) 一起工作
*   [通过 Web3 钱包连接了解 NextJS 13 中的水合错误](https://codingwithmanny.medium.com/understanding-hydration-errors-in-nextjs-13-with-a-web3-wallet-connection-8155c340fbd5)
*   [如何在 WebGL](/how-to-create-a-figma-like-infinite-canvas-in-webgl-8be94f65674f) 中创建类似 Figma 的无限画布
*   [状态共享的方法:React](https://medium.com/@godfreyyeung/approaches-to-state-sharing-react-95e5a41a3685) 作者 [Godfrey Yeung](https://medium.com/u/93250dc6ff27?source=post_page-----8e6d522bb738--------------------------------)
*   [Vite 真的比 Webpack 快吗？](/is-vite-really-faster-than-webpack-b414f6cc751c)作者[安东·卡尔曼诺维奇](https://medium.com/u/971c8a00db57?source=post_page-----8e6d522bb738--------------------------------)
*   [我比较了 Reflame、Vercel、Netlify、Cloudflare 页面在同一个 repo 上的部署速度](https://itnext.io/i-compared-deploy-speeds-for-reflame-vercel-netlify-cloudflare-pages-on-the-same-repo-377f74cdf2e3)
*   [使用 GitHub 的 REST API 和一个由](https://kymidd.medium.com/lets-do-devops-set-github-repo-permissions-on-hundreds-of-repos-using-github-s-rest-api-using-a-1bd880eae1ea) [Kyler Middleton](https://medium.com/u/7ef6d627a54c?source=post_page-----8e6d522bb738--------------------------------) 执行的 GitHub 操作在数百个回购上设置 GitHub 回购权限
*   [丹尼斯·亚科文科](/implementing-a-websocket-server-from-scratch-in-node-js-a1360e00a95f)[在 Node.js](https://medium.com/u/35c32b9c453a?source=post_page-----8e6d522bb738--------------------------------) 中实现一个 WebSocket 服务器
*   [用 Flet Python 框架](/building-a-markdown-editor-previewer-with-flet-7d9b06d6dc4b)构建一个 Markdown Editor Flutter 应用，作者 [Henri Ndonko](https://medium.com/u/3478410eee94?source=post_page-----8e6d522bb738--------------------------------)
*   你应该使用 Django 异步支持吗？作者[纳西尔·阿尔·希什曼](https://medium.com/u/5c929be3d2ff?source=post_page-----8e6d522bb738--------------------------------)
*   [用 Ktor 和 SQL delight PostgreSQL](/create-a-kotlin-native-web-server-with-ktor-and-sqldelight-postgressql-44485267c340)创建一个 Kotlin/Native Web 服务器

## ios

*   [SwiftUI 定制布局与简单布局引擎](https://medium.com/@chunkyguy/swiftui-custom-layout-with-simple-layout-engine-4196a0f219ee)由 [Sidharth Juyal](https://medium.com/u/b4ed78976849?source=post_page-----8e6d522bb738--------------------------------)
*   [建立一个金属数据库](https://medium.com/@mattpaletta/building-a-database-in-metal-b1b7cf280ba2)作者[马修·帕莱塔](https://medium.com/u/ea25e3a9f50d?source=post_page-----8e6d522bb738--------------------------------)
*   [金属三十天——第 29 天:基于物理的渲染](https://medium.com/@warrenm/thirty-days-of-metal-day-29-physically-based-rendering-e20e9c1bf984)作者[沃伦·摩尔](https://medium.com/u/548035e37c15?source=post_page-----8e6d522bb738--------------------------------)
*   [通过 ARKit 使用微笑控制 SwiftUI 视图](https://coledennis.medium.com/tutorial-control-a-swiftui-view-using-a-smile-through-arkit-42808233d523)由 [Cole Dennis](https://medium.com/u/9930541fe88e?source=post_page-----8e6d522bb738--------------------------------)
*   [如何用 SwiftUI 4](https://medium.com/@teresabagala/how-to-display-hierarchical-data-with-swiftui-4-5bc47c49082b) 显示分层数据
*   [SwiftUI 绑定扩展](/swiftui-binding-extensions-b6a9f27d2858)由[迈克尔龙](https://medium.com/u/ba1bf5213360?source=post_page-----8e6d522bb738--------------------------------)
*   [由](/building-ios-networking-app-with-automated-jwt-authorization-a14e02b512d0) [Lev Baklanov](https://medium.com/u/33a2591dd229?source=post_page-----8e6d522bb738--------------------------------) 创建一个具有自动 JWT 授权的 iOS 网络应用
*   [我的声音分析仪之旅](https://medium.com/@evelinwj2306/my-journey-with-sound-analyzer-ca118ae2e754)作者[伊芙琳](https://medium.com/u/86a947ed286d?source=post_page-----8e6d522bb738--------------------------------)

## 机器人

*   让水母在构图中移动:制作图像矢量动画并应用 AGSL 渲染效果
*   [如何用 NDK](/how-to-read-an-image-file-in-c-in-android-with-ndk-5d837039da3a) 在 Android 中用 C++读取一个图像文件
*   [通过](/understanding-gradle-tasks-417d6f5e13f4)[德米特里·列昂诺夫](https://medium.com/u/52427ca6a38a?source=post_page-----8e6d522bb738--------------------------------)了解 Gradle 任务
*   [你必须是 64 位才能乘坐这个摆渡](/you-must-be-64-bit-to-ride-this-ferry-61ed45c57a93)由[乔伊·瓦茨](https://medium.com/u/ac1f338e813b?source=post_page-----8e6d522bb738--------------------------------)
*   [在 Jetpack Compose 中创建可重用的动作菜单](https://fvilarino.medium.com/creating-a-reusable-actions-menu-in-jetpack-compose-95aec8eeb493)作者 [Francesc Vilarino Guell](https://medium.com/u/d814f16b5155?source=post_page-----8e6d522bb738--------------------------------)

# 工作

![](img/58318a57fac4d6af844847991483a4e1.png)

最后，我们得到了各种各样的工作经验——比如隐藏在酷玩乐队歌词中的[丽莎·施密特](https://medium.com/u/1e99abe3969f?source=post_page-----8e6d522bb738--------------------------------)的[与职业相关的建议。这里有几件让我们印象深刻的作品:](/5-pieces-of-career-advice-hidden-in-coldplay-lyrics-3b844297fa82)

*   [你应该在求职面试中问的一个问题](/the-one-question-you-should-ask-in-a-job-interview-99fc214f60df)
*   编码训练营 3 年后:毕业生的建议作者 [Iouri Sorokine](https://medium.com/u/2abde4b41ce1?source=post_page-----8e6d522bb738--------------------------------)
*   [40 岁时成为一名独立的软件企业家](/being-a-solo-software-entrepreneur-at-40-f2b397e6ba7f)由[亚历克斯·铃木](https://medium.com/u/e74a94afed37?source=post_page-----8e6d522bb738--------------------------------)
*   [如何把人放在绩效评估的中心](/how-to-put-people-at-the-center-of-performance-reviews-b8276cddf4b2)作者 [Vinita](https://medium.com/u/b892e7626234?source=post_page-----8e6d522bb738--------------------------------)
*   我已经被解雇 5.5 次了。下面是我从瓦西里那里学到的 4.5 课
*   [我们如何与 Scrum 合作](/how-we-work-with-scrum-ce46356879d2)作者 [Dafna Rosenblum](https://medium.com/u/68a23010fc3d?source=post_page-----8e6d522bb738--------------------------------)
*   [采访领导的 10 个深思熟虑的问题](/10-thoughtful-questions-for-interviewing-leaders-bf9a5658850f)作者[理查德·班菲尔德](https://medium.com/u/8b1bfe45609e?source=post_page-----8e6d522bb738--------------------------------)
*   [软件职业的真实资历](/true-seniority-67b2e01cce81)作者[Christoph Nile](https://medium.com/u/4221fe88c750?source=post_page-----8e6d522bb738--------------------------------)
*   [6 个简单的方法来显著改善你的项目计划](https://medium.com/@elliotgraebert/6-easy-practices-to-significantly-improve-your-project-planning-9bc989ddee2f)作者 [Elliot Graebert](https://medium.com/u/fd20f381e372?source=post_page-----8e6d522bb738--------------------------------)

这就结束了这个问题。直到下一次，

Anupam 和更好的编程团队