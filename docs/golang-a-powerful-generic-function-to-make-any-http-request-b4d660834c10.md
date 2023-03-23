# 实现一个 Golang 通用函数来处理任何 HTTP 请求

> 原文：<https://betterprogramming.pub/golang-a-powerful-generic-function-to-make-any-http-request-b4d660834c10>

## 这个强大的函数可以处理任何 HTTP 请求！

![](img/2bcbd0caddcb7aba45abeeb15ab548be.png)

在我在 [InClub](https://inclub-app.com/) 工作期间，我构建了一个通用的 HTTP 请求助手函数，我们可以在后台使用它。到目前为止，这个函数对我们抛出的每种类型的 HTTP 调用都有效。一些例子:

*   `POST` ing to [DeepL 的翻译文本 API](https://www.deepl.com/docs-api/translate-text/)
*   `GET` / `POST`到 [Instagram 的图形 API](https://developers.facebook.com/docs/instagram-api/)
*   `GET` / `POST` / `DELETE`至[仙鸟聊天平台 API](https://sendbird.com/docs/chat/v3/platform-api/overview)

我想我应该与世界分享这个函数，因为它是如何使用 Go 的通用功能的一个很好的真实例子。我将对如何构建这个函数做一个完整的演示，但是如果您只是想要这个函数，请跳到“完整代码片段”部分。

在这篇文章的其余部分，我将假设我们在一个名为`http_helper`的包中编写通用函数。这是在示例存储库中使用的名称。

# 入门-函数签名

让我们从函数的签名开始。我们把函数命名为`MakeHTTPRequest`；这很简单。这是一个非常灵活的函数，但是我们有一系列参数要接受:

*   我们需要期望的端点，我们称之为`fullUrl`，类型`string`
*   我声称我们可以处理`GET` s、`POST` s 和任何其他 HTTP 方法，我们还需要接受一个 HTTP 方法作为参数——我们称它为`httpMethod`，类型`string`(参见下面我们为什么选择使用`string`)
*   `headers`，键入`map[string]string`以应用于 HTTP 调用
*   查询参数(在`GET`的情况下)，称为`queryParameters`，类型为`url.Values`，
*   一个主体(在`POST`或`PUT`的情况下)，称为`body`，类型为`io.Reader`
*   最后，泛型开始为我们期望得到的类型服务——我们称之为`T`
*   对于返回类型，我们必须处理任何可能发生的错误，所以我们将使用经典的 go 模式，返回一个`error`作为最后的返回值

总之，这个强大函数的签名如下所示:

```
func MakeHTTPRequest[T any](fullUrl string, httpMethod string, headers map[string]string, queryParameters url.Values, body io.Reader, responseType T) (T, error)
```

# 编写函数体

让我们进入函数体。为了构建 HTTP 客户端，我们将使用 Go 的内置`net/http`包:

```
client := http.Client{}
```

然后我们需要解析 URL 以确保它是有效的:

```
u, err := url.Parse(fullUrl)
if err != nil {
  return responseType, err
}
```

现在我们可以处理 GET 情况，使用这个 URL 变量`**u**`来添加查询参数:

```
// if it's a GET, we need to append the query parameters.
if httpMethod == "GET" {
    q := u.Query()

    for k, v := range queryParameters {
        // this depends on the type of api, you may need to do it for each of v
        q.Set(k, strings.Join(v, ","))
    }
    // set the query to the encoded parameters
    u.RawQuery = q.Encode()
}
```

我们可以通过传入参数`body`来创建请求:

```
// regardless of GET or POST, we can safely add the body
req, err := http.NewRequest(httpMethod, u.String(), body)
if err != nil {
    return responseType, err
}
```

现在我们可以添加标题了:

```
// for each header passed, add the header value to the request
for k, v := range headers {
  req.Header.Set(k, v)
}
```

使用`net/http`的`Do`函数，我们可以发出请求:

```
// finally, do the request
res, err := client.Do(req)
```

然后，我们进行各种检查来验证请求是否成功:

```
if err != nil {
  return responseType, err
}
if res == nil {
  return responseType, fmt.Errorf("error: calling %s returned empty response", u.String())
}
responseData, err := io.ReadAll(res.Body)
if err != nil {
  return responseType, err
}
defer res.Body.Close()
if res.StatusCode != http.StatusOK {
  return responseType, fmt.Errorf("error calling %s:\nstatus: %s\nresponseData: %s", u.String(), res.Status, responseData)
}
```

我们终于到了这个函数强大的通用部分。我们使用`encoding/json`包将响应数据解组成我们期望的类型。如果`encoding/json`包无法解组到指定的类型，我们会抛出一个错误:

```
var responseObject T
err = json.Unmarshal(responseData, &responseObject)
if err != nil {
  log.Printf("error unmarshaling response: %+v", err)
  return responseType, err
}
```

如果解组成功，我们可以返回响应对象和一个`**nil**`错误:

```
return responseObject, nil
```

# 完整的代码片段

就是这样！我们构建了一个通用函数，可以处理任何类型的 HTTP 调用。以下是完整的代码:

```
package http_helper

import (
 "encoding/json"
 "fmt"
 "io"
 "log"
 "net/http"
 "net/url"
 "strings"
)

// in the case of GET, the parameter queryParameters is transferred to the URL as query parameters
// in the case of POST, the parameter body, an io.Reader, is used
func MakeHTTPRequest[T any](fullUrl string, httpMethod string, headers map[string]string, queryParameters url.Values, body io.Reader, responseType T) (T, error) {
 client := http.Client{}
 u, err := url.Parse(fullUrl)
 if err != nil {
  return responseType, err
 }

 // if it's a GET, we need to append the query parameters.
 if httpMethod == "GET" {
  q := u.Query()

  for k, v := range queryParameters {
   // this depends on the type of api, you may need to do it for each of v
   q.Set(k, strings.Join(v, ","))
  }
  // set the query to the encoded parameters
  u.RawQuery = q.Encode()
 }

    // regardless of GET or POST, we can safely add the body
 req, err := http.NewRequest(httpMethod, u.String(), body)
 if err != nil {
  return responseType, err
 }

 // for each header passed, add the header value to the request
 for k, v := range headers {
  req.Header.Set(k, v)
 }

    // optional: log the request for easier stack tracing
 log.Printf("%s %s\n", httpMethod, req.URL.String())

 // finally, do the request
 res, err := client.Do(req)
 if err != nil {
  return responseType, err
 }

 if res == nil {
  return responseType, fmt.Errorf("error: calling %s returned empty response", u.String())
 }

 responseData, err := io.ReadAll(res.Body)
 if err != nil {
  return responseType, err
 }

 defer res.Body.Close()

 if res.StatusCode != http.StatusOK {
  return responseType, fmt.Errorf("error calling %s:\nstatus: %s\nresponseData: %s", u.String(), res.Status, responseData)
 }

 var responseObject T
 err = json.Unmarshal(responseData, &responseObject)

 if err != nil {
  log.Printf("error unmarshaling response: %+v", err)
  return responseType, err
 }

 return responseObject, nil
}
```

# 用法示例

## 获取请求

```
package main

import (
    "net/url"
    "http_helper/http_helper"
)

func main() {
    // the url to call
    url := "https://api.github.com/users/alexellis"

    // the headers to pass
    headers := map[string]string{
        "Accept": "application/vnd.github.v3+json",
    }

    // the query parameters to pass
    queryParameters := url.Values{}
    queryParameters.Add("per_page", "1")

    // the type to unmarshal the response into
    var response map[string]interface{}

    // call the function
    response, err := http_helper.MakeHTTPRequest(url, "GET", headers, queryParameters, nil, response)
    if err != nil {
        panic(err)
    }

    // do something with the response
    fmt.Printf("response: %+v", response)
}
```

## 发布请求

```
package main

import (
    "bytes"
    "net/url"
    "http_helper/http_helper"
)

func main() {
    // the url to call
    url := "https://api.github.com/users/alexellis"

    // the headers to pass
    headers := map[string]string{
        "Accept": "application/vnd.github.v3+json",
    }

    // the query parameters to pass
    queryParameters := url.Values{}
    queryParameters.Add("per_page", "1")

    // the body to pass
    body := bytes.NewBufferString(`{"name": "test"}`)

    // the type to unmarshal the response into
    var response map[string]interface{}

    // call the function
    response, err := http_helper.MakeHTTPRequest(url, "POST", headers, queryParameters, body, response)
    if err != nil {
        panic(err)
    }

    // do something with the response
    fmt.Printf("response: %+v", response)
}
```

# 示例存储库

通用函数以及上面的用例可以在范例库中找到[。关于运行代码的最新说明可以在自述文件中找到。](https://github.com/princefishthrower/golang-generic-http-helper-function.git)

# 额外收获:正确检查或键入`httpMethod`参数

正如我们在最初的实现中看到的，即使在官方的 go 类型化中，HTTP 方法也只是简单的类型化。我们决定不执行任何检查的原因是，只有五种方法- `GET`、`POST`、`PUT`、`PATCH`和`DELETE`，而且它们不太可能在短期内改变！然而，它*是*当然，当调用这个函数(即`GET`)时，仍然有可能在方法字段上出现拼写错误，所以你和你的团队可能会决定使类型更严格一点。你有两个选择:

## 备选方案 1。简单的选择——正则表达式

将传递的字符串与所有五个 HTTP 方法的正则表达式进行匹配。这是一个不太复杂的选项，但是它不是类型安全的。我们可以使用下面的正则表达式:

```
// compile regex to test httpMethod
regex := regexp.MustCompile(`^(GET|POST|PUT|PATCH|DELETE)$`)

// check if httpMethod is valid
if !regex.MatchString(httpMethod) {
    return responseType, fmt.Errorf("invalid HTTP method: %s", httpMethod)
}
```

## **选项 2。更复杂的选项-枚举**

我们可以像这样创建一个 enum 和`String`方法:

```
type HTTPMethod int

const (
 GET HTTPMethod = iota
 POST
 PUT
 PATCH
 DELETE
)

func (s HTTPMethod) String() string {
 switch s {
 case GET:
  return "GET"
 case POST:
  return "POST"
 case PUT:
  return "PUT"
 case PATCH:
  return "PATCH"
 case DELETE:
  return "DELETE"
 }
 return "unknown"
}
```

然后，将`httpMethod`的类型从`string`更改为`HTTPMethod`:

```
func MakeHTTPRequest[T any](fullUrl string, httpMethod HTTPMethod, headers map[string]string, queryParameters url.Values, body io.Reader, responseType T) (T, error) {
```

我们必须修改`if`语句来比较新的`GET`类型，而不是字符串`"GET"`:

```
if httpMethod == GET {
  // ...code here...
}
```

当我们调用`http.NewRequest`时，我们需要将`httpMethod`转换成一个字符串:

```
req, err := http.NewRequest(string(httpMethod), u.String(), body)
```

选项#2 可能有点过度设计，但它也是最类型安全的。你想用哪个由你自己决定。如上所述，我们的团队已经决定放弃对`httpMethod`参数的任何检查，因为我们在我们的后端进行了广泛的[单元和集成测试](http://localhost:8000/blog/one-year-as-chief-technology-officer-at-inclub-insights-from-startup-land/)，通过请求错误的方式，可以捕捉到传递给`MakeHTTPRequest`的 HTTP 方法中的任何打字错误。

我希望你喜欢这篇文章！我真的很喜欢用 Go 构建后端基础设施是如此的简单。我期待着在未来发布更多的 Go 内容。

想用本文中使用的通用软件技能来提升你的游戏吗？

查看我的课程《高级打字稿:通用搜索、排序和过滤》，可以在 [Udemy](https://www.udemy.com/course/advanced-typescript-generic-search-sorting-and-filtering/?referralCode=22441D8B6B06045473D2) 和 [Skillshare](https://skl.sh/3EqkUTz) 上找到。