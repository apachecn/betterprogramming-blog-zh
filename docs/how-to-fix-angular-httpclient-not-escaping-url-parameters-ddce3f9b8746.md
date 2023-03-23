# 如何修复 Angular HttpClient 不转义 URL 参数

> 原文：<https://betterprogramming.pub/how-to-fix-angular-httpclient-not-escaping-url-parameters-ddce3f9b8746>

![](img/27f18382048e56cae4074b29515cd98b.png)

## 创建自定义的 HttpParameterCodec 来编码所有内容

Angular [Http 客户端](https://angular.io/api/common/http/HttpClient)是在 Angular 4.3 中引入的，在大多数方面都比旧的 Http 更好。它自动使用 JSON 作为内容类型，它支持 Http 拦截器，并且它比旧的 HTTP 具有更好的 TypeScript 支持。

最近，我需要向一个 HTTP 请求添加查询参数。对于这个用例，Angular 提供了`[HttpParams](https://angular.io/api/common/http/HttpParams)`类。奇怪的是，我注意到我的查询参数没有按预期编码，这使得 API 返回错误的结果。

这种行为可能会令人困惑:`[HttpUrlEncodingCodec](https://angular.io/api/common/http/HttpUrlEncodingCodec)`不编码某些特殊字符，如 *+* 。这可能符合 [RFC 3986](https://tools.ietf.org/html/rfc3986) 。但是，有些情况下您需要对这些字符进行编码。在本文中，我想向您展示如何做到这一点。

# 如何用 Angular 编码 HttpParams

1.  创建一个实现`[HttpParameterCodec](https://angular.io/api/common/http/HttpParameterCodec)`的类。或者，您也可以扩展`[HttpUrlEncodingCodec](https://angular.io/api/common/http/HttpUrlEncodingCodec)`。
2.  `encodeKey`和`encodeValue`函数需要对数值进行编码。普通 JavaScript 允许你使用`[encodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)`。
3.  创建新的`HttpParams` : `new HttpParams({ encoder: new CustomHttpParamEncoder() })`时使用自定义编码器类。重要的部分是`[encodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)`的用法，这是一个普通的 JavaScript 函数，用于对字符串进行编码。

以下是自定义解码器的外观:

```
import { HttpParameterCodec } from '@angular/common/http';export class CustomHttpParamEncoder implements HttpParameterCodec { encodeKey(key: string): string {
    return encodeURIComponent(key);
  } encodeValue(value: string): string {
    return encodeURIComponent(value);
  } decodeKey(key: string): string {
    return decodeURIComponent(key);
  } decodeValue(value: string): string {
    return decodeURIComponent(value);
  }}
```

## 结论

感谢阅读这篇文章。虽然 Http 客户端的行为初看起来令人惊讶，但它符合 RFC。幸运的是，很容易改变行为来满足你的需求。如果这篇文章对你有帮助，请在评论中告诉我。