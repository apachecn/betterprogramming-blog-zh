# Go 中的无中断 TLS 证书轮换

> 原文：<https://betterprogramming.pub/hitless-tls-certificate-rotation-in-go-8f24a26ec267>

## 在本教程中，我们将讨论如何在 Golang 中不停机地处理证书轮换。

![](img/359bcc1c03582f787cb90ca1a42365a1.png)

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# **什么是证书轮换，为什么需要证书轮换？**

证书轮换意味着用新证书替换现有证书。

有一句古老的 TLS 格言说:

> 如果颁发了证书，则必须对其进行轮换。

嗯，在以下情况下需要:

*   任何证书都会过期。
*   新的 CA 机构取代了旧的机构；因此需要为所有服务替换根证书。
*   需要对一个或多个证书施加新的或修改的约束。
*   出现安全漏洞，现有证书链不再可信。

手动轮换 TLS 证书可能会很快失去控制，特别是当您必须管理数百个证书时，如果您颁发的证书在几小时内而不是几个月内过期，就会变得完全无法管理。

`[tlsreconciler](https://github.com/shaj13/tlsreconciler)`通过重新加载包括根 CA 在内的循环证书，并实时为连接提供 TLS 协调，无需重启应用程序，来帮助解决这一问题。

# **代码**

我们将从创建我们的项目开始。

```
mkdir medium && cd medium && go mod init medium && touch main.go
```

这将创建一个名为“medium”的新目录，并用`go.mod`初始化项目。

我们还需要安装`[tlsreconciler](https://github.com/shaj13/tlsreconciler)`。

```
go get github.com/shaj13/tlsreconciler
```

在我们写任何**证书轮换**代码之前，我们需要写一些描述我们的教程程序的强制代码。

```
package main

import (
	"crypto/tls"
         "log"
	"net/http")

func HelloMedium(w http.ResponseWriter, req *http.Request) {
	w.Header().Set("Content-Type", "text/plain")
	w.Write([]byte("Hello Medium.\n"))
}

func main() {

	config := new(tls.Config)
	server := http.Server{
		Addr:      ":443",
		Handler:   http.HandlerFunc(HelloMedium),
		TLSConfig: config,
	}

  	err := server.ListenAndServeTLS("", "")
        if err != nil {
            log.Fatal(err)
        }}
```

运行该程序将返回以下错误。

```
open : no such file or directory
```

基本上，这个错误从`http.ListenAndServeTLS`返回，因为我们已经用空的`tls.Config`初始化了服务器，所以它退回到未定义的证书路径参数，稍后将修复它。

我们需要让我们的程序捕捉`SIGHUB`，这样我们就可以重新加载旋转后的证书。

这是可以做到的，就像这样:

```
sigc := make(chan os.Signal, 1)
defer close(sigc)

signal.Notify(sigc, syscall.SIGHUP)
```

现在，我们需要配置`tlsreconciler`，就像这样。

```
sig := tlsreconciler.WithSIGHUPReload(sigc)
certs := tlsreconciler.WithCertificatesPaths("./cert", "./key", "")
cb := tlsreconciler.WithOnReload(func(c *tls.Config) {
      log.Println("TLS certificates rotated !!")
})
```

上面的代码创建了一个`tlsreconciler`配置，在收到一个`SIGHUB`时重新加载证书，并调用我们的回调函数打印一个漂亮的日志条目，从给定的路径重新加载证书。

是时候把这些点连接起来了，我们需要替换下面这条线:

```
config := new(tls.Config)
```

从`tlsreconciler`返回`tls.Config`:

```
config := tlsreconciler.TLSConfig(sig, certs, cb)
```

您的文件现在应该看起来像这样。

# **测试我们的代码**

首先让我们生成一个证书和私钥:

```
openssl genrsa -out ./key 2048
openssl req -new -x509 -sha256 -key ./key -out ./cert -days 3650
```

现在让我们运行我们的程序:

```
go build main.go
./main 
```

让我们从不同的 shell 发送一个 HTTPS 请求:

```
curl -k https://127.0.0.1:443/
```

在程序日志中，你会发现下面的日志条目“`TLS certificates rotated !!`”。

出现这种情况是因为`tlsreconciler`第一次重新加载了证书，运行更多的 curls 将不再打印上面的日志条目。

现在让我们检查旋转，为此我们将使用`openssl`来显示证书

```
openssl s_client -showcerts -connect 127.0.0.1:443 2>&1|openssl x509 -noout -serial
```

上述命令将打印证书序列号。

让我们通过使用`openssl`生成新的证书并将`SIGHUP`发送到我们的程序来轮换证书

```
rm -rf ./key ./cert 
openssl genrsa -out ./key 2048
openssl req -new -x509 -sha256 -key ./key -out ./cert -days 3650 
kill -SIGHUP <PID>
```

现在，让我们验证序列号是否与之前的序列号不同

```
openssl s_client -showcerts -connect 127.0.0.1:443 2>&1|openssl x509 -noout -serial
```

我希望这篇文章对你有所帮助。更多关于`tlsreconciler`的内容可以在[*GitHub*](https://github.com/shaj13/tlsreconciler)*和 [*GoDoc*](https://pkg.go.dev/github.com/shaj13/tlsreconciler) *上找到。**