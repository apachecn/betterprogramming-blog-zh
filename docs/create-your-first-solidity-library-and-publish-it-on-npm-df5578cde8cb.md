# 创建您的第一个 Solidity 库，并在 NPM 上发布

> 原文：<https://betterprogramming.pub/create-your-first-solidity-library-and-publish-it-on-npm-df5578cde8cb>

## 与世界分享你的固体图书馆

![](img/689c6a431ff2397bc6ed057faaf2ba3c.png)

照片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你写了 Solidity 代码，你很可能使用了一个现有的契约/库，很可能是来自`openzeppelin`的东西。类似于:

```
import "@openzeppelin/contracts/utils/Strings.sol";
```

今天我们将介绍如何创建自己的 Solidity 库并将其发布到 NPM 上。

# 步骤 1:初始化存储库

在编写任何 Solidity 代码之前，让我们先创建我们的目录。我假设您使用的是 Mac/Linux，那么我们可以通过以下方式创建我们的目录:

```
mkdir YOUR_DIR_NAME
```

假设您已经安装了`npm`,我们现在可以通过以下方式在其中初始化一个 npm 目录:

```
cd YOUR_DIR_NAME
npm init -y
```

# 步骤 2:编写可靠性代码

一旦你有了回购，我们现在可以创建我们的第一个 Solidity 库。Solidity 中的库需要`library`关键字而不是`contract` define。

对于本教程，我们将创建一个只有一个函数的库。这个函数将两个字符串连接在一起。这是我想出的代码:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

library Concatenator {
    function concatenate(string calldata _str, string calldata _str1)
      public pure returns(string memory)
    {
        return string(abi.encodePacked(_str, _str1));
    }
}
```

# 步骤 3:发布和使用库

完成后，现在只需运行以下命令就可以发布这个库了:

```
npm publish
```

如果您没有在终端中登录到`npm`帐户，您可能需要通过以下方式登录:

```
npm adduser
```

搞定了。现在，您的图书馆可供全球数百万 NPM 用户使用！

# 测试您的库

如果您想测试您的库，您可以通过编写一个基本契约来完成，例如:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "YOUR_DIR_NAME/Concatenator.sol";

contract Tester {
    using Concatenator for string;
    function concatenate(string calldata _str, string calldata _str1) public pure returns(string memory) {
        return _str.concatenate(_str1);
    }
}
```

如果你喜欢这个，你可能会喜欢 Twitter 上的更高频率的内容。我很期待看到你们想出了哪些很酷的库！

感谢阅读。