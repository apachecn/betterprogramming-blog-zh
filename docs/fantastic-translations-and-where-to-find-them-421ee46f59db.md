# 如何在 Angular 中使用 Ngx-Translate？

> 原文：<https://betterprogramming.pub/fantastic-translations-and-where-to-find-them-421ee46f59db>

## 精彩的翻译以及在哪里可以找到它们

![](img/27a931ae80e7d2c33fb21f2509585126.png)

全世界的人都说不同的语言

## 如何使用静态翻译文件在 Angular 中设置翻译

如果你生活在一个说多种语言的国家，你不会想设计一个只针对一种语言的网站。

无论你是在经营一家企业还是像我一样创建一个网站，如果访问者必须努力翻译你的内容，他们很可能不会在你的网站上花太多时间。我想分享我在 Angular 中设置翻译的经验，并给出一些代码片段来使事情更清楚。

您应该首先在 assets 文件夹中创建一些语言文件。这些翻译以简单和特定的格式存储。首先，您编写密钥，然后提供正确的翻译值。我只给出了一个英文翻译文件`en.json`的例子，但是你可以随意添加。只为您的目标受众添加语言。

```
{  
  "**current.language**": "en",
  "**a.message**": "My awesome message",
  ***...***
}
```

嵌套键和值在技术上是可行的，但如果我是你，我不会这么做。我的第一个原因是，在编辑器中搜索关键字变得很困难。这也意味着不能再复制粘贴密钥；您仍然可以在像 Intellij 这样的优秀编辑器中复制引用。

您还需要一个数据结构来表示项目中选择的语言。在我们的应用程序中，你总是有两种类型的语言:一种是当前活动的语言。下面的代码将允许我们简单地构造一个类似于 `Languages.from(current, supportedLanguages)`的语言对象。很方便，不是吗？

```
export class **Languages** {
  readonly **current**: string;
  readonly **other**: string[]; private ***constructor***(currentLanguage: string,
                      supportedLanguages: string[]) {
    this.current = currentLanguage;
    this.other = supportedLanguages.filter(supportedLanguage => supportedLanguage !== currentLanguage);
  } static ***from***(currentLanguage: string, supportedLanguages: string[]): Languages {
    return new Languages(currentLanguage, supportedLanguages);
  }
}
```

接下来你需要的是提供你的翻译服务。它根据请求接收任何语言的静态翻译。这意味着语言是“延迟加载”的——您不需要在页面加载时请求每一种语言，而是只请求初始语言和语言变化时的语言。

```
@Injectable({
  providedIn: 'root'
})
export class **TranslationService** {
  **constructor**(private httpClient: HttpClient) {} ***getTranslation***(language: string): Observable<TranslationKeyValues>     {
  return from(this.getLanguageTranslationsRequest(language));
  } private ***getLanguageTranslationsRequest***(language: string): Promise<TranslationKeyValues> {
    return this.httpClient.get<TranslationKeyValues>(`assets/translation/${language}.json`).toPromise();
  }
}
```

您可能想知道，它是如何获得特定的返回类型的？我定义了类型`TranslationKeyValues`，它只是键值的映射。这种数据类型只是语言文件翻译的表示。

```
export interface **TranslationKeyValues** {
  [key: string]: string;
}
```

现在您只需要用我们的值填充翻译模块。这可以通过实现我们自己的`CustomTranslateLoader`来轻松完成，T3 实现了`TranslateLoader`接口。每当这个类需要新的翻译时，它就会调用我们的翻译服务。

```
export class **CustomTranslateLoader** implements **TranslateLoader** {
  private constructor(private **translationService**: TranslationService) {
  } static ***of***(translationService: TranslationService) {
    return new CustomTranslateLoader(translationService);
  } ***getTranslation***(language: string): Observable<TranslationKeyValues> {
    return this.translationService.getTranslation(language);
  }
}
```

我已经将 translate loader 包装在导出的函数(工厂方法)中，但是您不必这样做。但是，在使用工厂的导出函数进行提前编译时，它是必需的。

```
export function ***CustomTranslateLoaderFactory***(translationService: TranslationService) {
  return CustomTranslateLoader.of(translationService);
}
```

您现在可以在我们的`TranslationModule` 中提供一个自定义的`TranslateLoader` 使用工厂，其中有您之前定义的`TranslationService` 作为依赖项。

```
@NgModule({
  imports: [
    **TranslateModule**.forRoot({
      loader: {
        provide: **TranslateLoader**,
        useFactory: **CustomTranslateLoaderFactory**,
        deps: [
          **TranslationService**
        ]
      }
    })
  ],
  providers: [
    CookieService
  ],
  exports: [
    **TranslateModule**
  ]
})
export class **TranslationModule** {}
```

为了改变你的语言，我做了一个简单的服务，允许切换。如果你喜欢，可以随意使用或更改它。现在一切就绪！您已经成功地在 Angular 中设置了您的翻译。

```
/* 
Just call this.languageService.updateCurrentLanguage(language); to switch language 
*/@Injectable({
  providedIn: 'root'
})
export class LanguageService {
  private readonly **LANGUAGE_COOKIE** = 'website.locale';
  private **languagesSubject**: BehaviorSubject<Languages>; ***constructor***(private **translateService**: TranslateService, private **cookieService**: CookieService) {
    const currentLanguage = cookieService.get(this.LANGUAGE_COOKIE) || this.translateService.getDefaultLanguage();
    this.languagesSubject = new BehaviorSubject<Languages>(this.getLanguages(currentLanguage));
    this.translateService.use(currentLanguage);
  } ***getCurrentAndOtherLanguages$***(): Observable<Languages> {
    return this.languagesSubject.asObservable();
  } ***updateCurrentLanguage***(currentLanguage: string): void {
    this.cookieService.set(this.LANGUAGE_COOKIE, currentLanguage);
    this.translateService.use(currentLanguage);
    this.languagesSubject.next(this.getLanguages(currentLanguage));
  } private ***getLanguages***(currentLanguage: string): Languages {
    return Languages.from(currentLanguage, this.translateService.getSupportedLanguages());
  }
}
```

现在你可以在任何地方使用翻译服务，就像这样简单。您甚至可以在翻译中提供参数。当你改变语言时，最方便的部分开始了——翻译自动改变。关于如何使用翻译的更多信息，我推荐访问 **ngx-translate** 的 GitHub 页面[这里](https://github.com/ngx-translate/core)。

```
<div class="home">
  <p class="website">...</p>
  <h1 class="a">{{'a.message' | **translate**}}</h1>
  <p class="b">{{'b.message' | **translate**}}</p>
  <p class="c">{{'c.message' | **translate:param**}}</p>
</div>
```