# 安全服务让我们使用 DNS-01 挑战加密 SSL/TLS 证书

> 原文：<https://betterprogramming.pub/ssl-certificates-with-lets-encrypt-and-dns-01-3315a5a9f706>

## Go 中的一个实现

![](img/927fb9d632fcc49f3ff28633cd9efc96.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

现在是 2022 年，不安全的网站已经成为过去。大多数浏览器甚至不允许你连接到一个通过 HTTP 服务的网站。

然而，这并没有改变处理 SSL 证书可能很麻烦的事实。您有几个选择:

*   走传统的路，从众多提供商那里购买 SSL 证书
*   或者从[免费得到一个让我们加密](https://letsencrypt.org/)

> 我想指出的是，免费并不总是更好。不同的提供商提供不同级别的安全性和其他功能，这些我们加密可能没有。但是有些安全比没有安全好太多了！

你可能已经怀疑有一个陷阱。让我们加密发行有效期为 [90 天](https://letsencrypt.org/docs/faq/#what-is-the-lifetime-for-let-s-encrypt-certificates-for-how-long-are-they-valid)的证书，而传统提供商的有效期为[一年多一点](https://blog.mozilla.org/security/2020/07/09/reducing-tls-certificate-lifespans-to-398-days/)。后者确实使处理证书刷新过程更容易一些(因为您只需每周刷新一次，所以更快地刷新和过期证书实际上更安全，因为如果证书的私钥被第三方窃取，它会减少攻击发生的时间。

然而，这增加了[的工作量](https://sre.google/sre-book/eliminating-toil/)，因为下一次刷新总是即将到来。而且，您可能从多台服务器上为您的资产提供服务，增加了每次证书过期时所需的手动工作量，这种情况也不是没有过。

到目前为止，应该很清楚，与 Let's Encrypt 的任何交互都应该是自动化的。幸运的是，他们从一开始就支持这个流程，使用了 [ACME 协议](https://en.wikipedia.org/wiki/Automatic_Certificate_Management_Environment)(就是为了这个目的而发明的)。

如果你运行一个标准的基于 HTTP 的服务，网站等。，你会发现有大量的选项(工具)来解决这个问题:

*   [certbot](https://certbot.eff.org/) —参考 ACME 实现
*   [caddy](https://caddyserver.com/) —基于 Golang 的 HTTP 服务器
*   或者用你能想到的任何编程语言编写的数十种其他的实现

有一个问题— HTTP 是关键字。这些实现中的大多数(如果不是全部的话)都假设您正在运行一个 web 服务器，或者可以通过 HTTP 以某种方式公开一个服务，以便客户端可以确认它控制着为其颁发证书的域。

但是如果不能呢！？如果您只能公开一个端口(并且不是针对 HTTP 的)，或者如果您在一个有空隙的环境中运行一个服务，该环境只能连接到外部，而不允许传入流量，那该怎么办？

# 输入 DNS-01 挑战

根据“让我们加密文档”:

> 该挑战要求您通过在该域名下的 TXT 记录中输入一个特定值来证明您控制了该域名的 DNS。

我无法立即找到支持这一流程的客户端。这部分是因为 DNS 是一个协议，而不是一个 API。在我的例子中，[我使用 Cloudflare](https://mihaibojin.medium.com/hosting-my-site-on-vercel-7ea2235c2a78#bbb0) 管理我的所有域，所以我需要*一个能够在 Cloudflare 上更新 TXT 记录的 ACME 客户端—* 这不是我想到的第一个组合…

Cloudflare 是众多 DNS 托管平台中的一个——构建和维护一个涵盖大多数/所有此类平台的库的成本相对较高——我意识到我必须构建自己的库！

# 使用 Go 编写 ACME/Cloudflare 客户端

最近，我一直在学习围棋。它是一种非常流行的语言，拥有健康的开发者生态系统。我喜欢的是，通常很容易就能找到各种可以解决大多数问题的库，比如满足以下需求:

*   [acmez](https://pkg.go.dev/github.com/mholt/acmez) :用于 Go 的 ACME 客户端库
*   [libdns/cloudflare](https://pkg.go.dev/github.com/libdns/cloudflare) :使用 Cloudflare APIs 的 dns 提供商

让我们从一个简单的结构开始，来保存我们未来的 SSL/TLS 证书的配置。

```
type Config struct {
 CertificatePrivateKeyPath string
 CloudflareAPIToken        string
}
```

*   `ACMEAccountPrivateKeyPath`:您必须先在 Let's Encrypt 注册一个账号；我们的代码将生成密钥并存储在这个路径中
*   `CertificatePrivateKeyPath`:每个证书都需要一个私钥；我们将生成它并存储在这个路径中
*   `CertificatePath` *:* 我们将把从 Let's Encrypt 获得的证书存储在这个路径下
*   `CloudflareAPIToken`:一个 Cloudflare API 令牌，咄！

我们将通过一个简单的 API 与库进行交互:

```
func RequestCertificate(
  domains []string,
  ownerEmail []string,
  cfg *Config
) error {...}// called as
domains := []string{"example.com"}
emails := []string{"mailto:info@example.com"}
err := RequestCertificate(domains, emails, c)
if err != nil {
  ...
}
```

让我们从实现`RequestCertificate`开始:

```
ctx := context.Background()// We use Uber's Zap logger, as required by acmez
logger, _ := zap.NewProduction()
defer logger.Sync() // flushes buffer, if any// Initialize a DNS-01 solver, using Cloudflare APIs
solver := &certmagic.DNS01Solver{
  DNSProvider:        &cloudflare.Provider{APIToken: cfg.CloudflareAPIToken},
}// The CA endpoint to use (prod or staging)
// switch to Production once fully tested
// otherwise you might get rate-limited in Production
// before you've had a chance to test that your client
// works as expected
caLocation = certmagic.LetsEncryptStagingCA
//caLocation := certmagic.LetsEncryptProductionCA// CONTINUED BELOW ...
```

> 仅供参考， *acmez* 图书馆使用优步 [Zap 记录器](https://pkg.go.dev/go.uber.org/zap)。您可以跳过提供日志记录器，这意味着您不会从 ACME 客户端获得任何信息，从而错过了潜在的错误。

```
// Initialize an acmez client
client := acmez.Client{
 Client: &acme.Client{
  Directory: caLocation,
  UserAgent: "[SOMETHING TO IDENTIFY YOUR CLIENT]",
  Logger:    logger,
 },
 ChallengeSolvers: map[string]acmez.Solver{
  acme.ChallengeTypeDNS01: solver,
 },
}// Generate a private key for your Let's Encrypt account
accountPrivateKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
if err != nil {
 return fmt.Errorf("ecdsa.GenerateKey() could not generate an account key: %v", err)
}// Create a Let's Encrypt account
account := acme.Account{
 Contact:              ownerEmail,
 TermsOfServiceAgreed: true,
 PrivateKey:           accountPrivateKey,
}acc, _ := client.NewAccount(ctx, account)
if err != nil {
 return fmt.Errorf("client.NewAccount() could not create new account: %w", err)
}
```

此时，我们通过了 Let's Encrypt 的身份验证，并准备好发出证书请求。

```
// Generate a private key for the certificate
certPrivateKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
if err != nil {
 return fmt.Errorf("generating certificate key: %w", err)
}// TODO(left to the reader): store this key to a file// obtain certificates from Let's Encrypt
certs, err := client.ObtainCertificate(ctx, acc, certPrivateKey, domains)
if err != nil {
 return fmt.Errorf("client.ObtainCertificate() could not obtain certificate: %w", err)
}// since the client returns more than one cert, it is up to you
// to choose the most appropriate one (such as one which contains
// the full chain, including any intermediate certificates)
for _, cert := range certs {
 log.Println(string(cert.ChainPEM))
 // TODO(left to the reader): store cert.ChainPEM to a file
}return nil
```

我可以给你的一个提示是，要存储一个私钥，你必须首先把它转换成 ASN.1 DER 格式。这很容易实现；见下文:

```
func EncodeAndStorePrivateKey(privateKey *ecdsa.PrivateKey, filename string, mode fs.FileMode) error {
 x509Encoded, err := x509.MarshalECPrivateKey(privateKey)
 if err != nil {
  return err
 }data := pem.EncodeToMemory(&pem.Block{Type: "PRIVATE KEY", Bytes: x509Encoded})
 return os.WriteFile(filename, data, mode)
}
```

差不多就是这样了！现在，您可以通过使用 DNS-01 质询进行加密来发布和刷新 SSL/TLS 证书(如果您的域的 DNS 是在 [Cloudflare](https://www.cloudflare.com/) 上管理的)。

还有一件事；这里有一个要添加的 Go 导入的完整列表——当您将所有这些放在一起时，这会节省您一点时间！

```
import (
 "context"
 "crypto/ecdsa"
 "crypto/elliptic"
 "crypto/rand"
 "crypto/x509"
 "encoding/pem"
 "fmt"
 "io/fs"
 "log"
 "os" "github.com/caddyserver/certmagic"
 "github.com/libdns/cloudflare"
 "github.com/mholt/acmez"
 "github.com/mholt/acmez/acme"
 "go.uber.org/zap"
)
```

不要忘记`go get`上面列出的外部模块！

感谢阅读。让我知道你的想法，在推特上！