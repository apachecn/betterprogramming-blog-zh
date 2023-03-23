# 在 Python 中使用 Linux RSA 加密密钥

> 原文：<https://betterprogramming.pub/using-linux-rsa-encryption-keys-in-python-d9ab840a81e5>

## 为了保护您的最终用户

![](img/859f937e3860ce159a087ecd2cafdf4f.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Towfiqu barb huya](https://unsplash.com/@towfiqu999999?utm_source=medium&utm_medium=referral)拍摄的照片

加密在我们的日常应用程序开发中变得越来越重要。我们将探讨如何在 Linux 上加密数据，并在 Python 中解密。这将涉及创建一个私有和公共密钥，在 Linux 上用公共密钥加密数据，在 Python 应用程序中用私有密钥解密数据。

这带来了 Python 和 Linux 在创建或使用加密密钥方面的各种差异。

首先，我们将使用 OpenSSL Linux 包来生成密钥和加密数据，并使用 pycryptodome Python 库来解密。

然后，我们用公钥加密纯文本。

用于解密的 Python 库是 pycryptodome。按如下方式安装软件包:

```
python3 -m pip install pycryptodome
```

我们有必要的密钥和加密数据，这些数据将用 Python 解密。在这个 Python 脚本中，我们将读取加密文件并对数据进行 base64 解码，然后使用私钥和密码对其进行解密。私人空间在使用前必须清洁。为此，请执行以下操作:

*   读取私钥并删除私钥的开头和私钥注释的结尾
*   去掉键以删除不需要的空格和新的行字符
*   将密钥存储在一个字符串中
*   base64 解码密钥

然后，它可以与用于创建准备使用的私钥的密码一起加载到 RSA 导入密钥函数中。

因为我们在加密数据时选择了 OAEP 填充，所以我们将使用 PKCS1_OAEP 方法来读取私钥并创建一个密码。

最后，解密数据并进行 base64 解码，使其成为人类可读的格式。

有许多用例需要在应用程序之外创建一个密钥，并在应用程序中使用它来解密数据。

这篇文章希望能给你一个实现这个目标的思路。

完整的代码可以在我的 [GitHub 库](https://github.com/Wainaina3/publications.git)中找到。