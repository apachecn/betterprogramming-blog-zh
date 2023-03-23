# 在 Angular 中以正确的方式进行 API 调用

> 原文：<https://betterprogramming.pub/angular-api-calls-the-right-way-264198bf2c64>

## 进行 API 调用的最佳实践

![](img/a1a6820dbe4c882e47c2857b4762d811.png)

如果您正在创建一个 Angular 应用程序，您需要做的事情之一就是进行 API 调用。我将向您展示如何以正确的方式，避免 bug 或重复自己，并使升级到 Angular 的新版本变得更容易。

组件内部 API 调用的一个常见错误是:

```
this.httpClient.get('https://www.domain.com/api/data/' + this.id);
```

这是错误的，原因有三:

1.  您直接在组件中使用 API URL，如果您需要更改 API URL，您必须在每个组件中更改它。
2.  你在组件中直接使用 Angular 的`httpClient` ，如果 Angular 用另一个模块替换这个模块，你必须在每个组件中改变它。
3.  path 变量未编码。因此，如果一个字符不是有效的 URL 字符，API 调用将失败。

首先，你必须知道什么是`CoreModule` 和`SharedModule`。如果你不知道，请阅读[这篇](https://levelup.gitconnected.com/where-shall-i-put-that-core-vs-shared-module-in-angular-5fdad16fcecc)。

# 入门指南

在`src/app`里面，创建一个名为`config`的文件夹，里面有一个`constants.ts`文件。

```
// Angular Modules
import { Injectable } from '@angular/core';@Injectable()
export class Constants {
    public readonly API_ENDPOINT: string = 'domain/api';
    public readonly API_MOCK_ENDPOINT: string = 'mock-domain/api';
}
```

将该文件导入到`CoreModule`中。

我们有两个变量:一个用于实际的 API 端点，一个用于`mock` ——但是为什么呢？

大多数时候，API 模型已经完成，但是实际的调用并没有实现。为了不等待 API 调用的实现，您可以从 JsonGenerator 创建一个伪调用，并在您的应用程序中使用它。

现在，如果 API 的域被更改，您只需要更改上面的文件，就可以了。

# 创建一个`api-http.service.ts`文件

在`src/app/core/services`中，创建一个`api-http.service.ts`文件。

```
// Angular Modules
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';@Injectable()
export class ApiHttpService { constructor(
    // Angular Modules
    private http: HttpClient
  ) { } public get(url: string, options?: any) {
    return this.http.get(url, options);
  } public post(url: string, data: any, options?: any) {
    return this.http.post(url, data, options);
  } public put(url: string, data: any, options?: any) {
    return this.http.put(url, data, options);
  } public delete(url: string, options?: any) {
    return this.http.delete(url, options);
  }
}
```

将该文件导入到`CoreModule`中。

在 Angular 4.3 中，`HttpModule` 成为遗留，新的`HttpClientModule` 成为进行 API 调用的默认模块。如果类似的事情再次发生，您不需要在每个组件中改变模块的使用，而只需要在这个文件中改变。升级会更容易。

# 创建其他必需的文件

在`src/app/shared/classes`内部，创建一个`query-string-parameters.ts`文件。

```
export class QueryStringParameters {
  private paramsAndValues: string[]; constructor() {
    this.paramsAndValues = [];
  } public push(key: string, value: Object): void {
    value = encodeURIComponent(value.toString());
    this.paramsAndValues.push([key, value].join('='));
  } public toString = (): string => this.paramsAndValues.join('&');
}
```

这个类获取一个查询字符串键值集，并对该值进行编码。它还返回带有`&`字符的字符串中的所有查询字符串键值集:
例如`id=3&type=customer`。

在`src/app/shared/classes`里面，创建一个`url-builder.ts`文件。

```
// Application Classes
import { QueryStringParameters } from './query-string-parameters';export class UrlBuilder {
  public url: string;
  public queryString: QueryStringParameters; constructor(
    private baseUrl: string,
    private action: string,
    queryString?: QueryStringParameters
  ) {
    this.url = [baseUrl, action].join('/');
    this.queryString = queryString || new QueryStringParameters();
  } public toString(): string {
    const qs: string = this.queryString ?
                       this.queryString.toString() : '';
    return qs ? `${this.url}?${qs}` : this.url;
  }
}
```

这个类使用`QueryStringParameters`类来生成最终的 API URL:
例如，[https://domain.com/api/get-user?id=3&类型=客户](https://domain.com/api/get-user?id=3&type=customer)。

上述 URL 由三部分组成:

1.  基本网址:[https://domain.com/api](https://domain.com/api)
2.  动作:`get-users`
3.  查询字符串:`id=3&type=customer`

URL 构建器将这些作为参数来创建最终的 API URL。

在`src/app/core/services`中，创建一个`api-endpoints.service.ts`文件。

```
// Angular Modules
import { Injectable } from '@angular/core';
// Application Classes
import { UrlBuilder } from '../../shared/classes/url-builder';
import { QueryStringParameters } from '../../shared/classes/query-string-parameters';
// Application Constants
import { Constants } from 'src/app/config/constants';@Injectable()
export class ApiEndpointsService { constructor(
    // Application Constants
    private constants: Constants
  ) { } /* #region URL CREATOR */
  // URL
  private createUrl(
    action: string, 
    isMockAPI: boolean = false
  ): string {
    const urlBuilder: UrlBuilder = new UrlBuilder(
      isMockAPI ? this.constants.API_MOCK_ENDPOINT :   
      this.constants.API_ENDPOINT,
      action
    );
    return urlBuilder.toString();
  } // URL WITH QUERY PARAMS
  private createUrlWithQueryParameters(
    action: string, 
    queryStringHandler?: 
      (queryStringParameters: QueryStringParameters) => void
  ): string {
    const urlBuilder: UrlBuilder = new UrlBuilder(
      this.constants.API_ENDPOINT, 
      action
    );
    // Push extra query string params
    if (queryStringHandler) {
      queryStringHandler(urlBuilder.queryString);
    }
    return urlBuilder.toString();
  }

  // URL WITH PATH VARIABLES
  private createUrlWithPathVariables(
    action: string, 
    pathVariables: any[] = []
  ): string {
    let encodedPathVariablesUrl: string = '';
    // Push extra path variables
    for (const pathVariable of pathVariables) {
      if (pathVariable !== null) {
        encodedPathVariablesUrl +=
          `/${encodeURIComponent(pathVariable.toString())}`;
      }
    }
    const urlBuilder: UrlBuilder = new UrlBuilder(
      this.constants.API_ENDPOINT,  
      `${action}${encodedPathVariablesUrl}`
    );
    return urlBuilder.toString();
  }
  /* #endregion */
}
```

在`CoreModule`中导入该文件。

这项服务可以:

1.  创建一个使用真实或模拟 API 的 API URL。
2.  创建带有查询字符串的 API URL。
3.  用路径变量创建一个 API URL。

所有的 API URLs 都将从此服务中提供。让我们在服务内部创建一些 URL。

```
public getNewsEndpoint(): string {
  return this.createUrl('news');
}
```

此方法将返回:
https://domain.com/api/news

```
public getNewsEndpoint(): string {
  return this.createUrl('news', true);
}
```

这个方法将返回:
https://mock-domain.com/api/news

```
public getProductListByCountryAndPostalCodeEndpoint(
  countryCode: string, 
  postalCode: string
): string {
  return this.createUrlWithQueryParameters(
    'productlist', 
    (qs: QueryStringParameters) => {
      qs.push('countryCode', countryCode);
      qs.push('postalCode', postalCode);
    }
  );
}
```

这个方法将返回:
https://domain.com/api/productlist?countrycode=en&postal code = 12345

```
public getDataByIdAndCodeEndpoint(
  id: string,
  code: number
): string {
  return this.createUrlWithPathVariables('data', [id, code]);
}
```

这个方法将返回:
https://domain.com/api/data/12/67

现在，让我们转到一个组件，一起使用它们。

```
constructor(
  // Application Services
  private apiHttpService: ApiHttpService,
  private apiEndpointsService: ApiEndpointsService
) {ngOnInit() {
  this.apiHttpService
      .get(this.apiEndpointsService.getNewsEndpoint())
      .subscribe(() => {
        console.log('News loaded'))
      });
}
```

以上所有都是大规模应用程序所必需的，但即使对于小的应用程序，它们也是有用的。

# 资源

这段代码可以在 GitHub [这里](https://github.com/georgeroubie/Angular-API-calls-the-right-way)找到。