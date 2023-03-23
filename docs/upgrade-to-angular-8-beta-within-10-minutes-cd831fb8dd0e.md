# 10 分钟内从 Angular 7 升级到 Angular 8

> 原文：<https://betterprogramming.pub/upgrade-to-angular-8-beta-within-10-minutes-cd831fb8dd0e>

## 升级到 Angular 的最新版本比你想象的要容易

![](img/1a0fbcef469764b1ee4e670fce666cbc.png)

跟随这篇短文，你可以将你的 Angular 7 项目升级到新的 Angular 8。您需要安装所有带有`@8.0.0`标签的角度依赖项。我只是给你必要的信息，没有废话，所以你会很快升级！

[](https://medium.com/@jeroenouw/upgrade-to-angular-9-within-10-minutes-671c6fd6174b) [## 10 分钟内升级到 Angular 9

### 现在轻松地将您的项目更新到 Angular 的新版本

medium.com](https://medium.com/@jeroenouw/upgrade-to-angular-9-within-10-minutes-671c6fd6174b) 

# 您需要安装以下依赖项

## 角度依赖性

```
npm install @angular/animations@8.0.0 @angular/common@8.0.0 @angular/compiler@8.0.0 @angular/core@8.0.0 @angular/forms@8.0.0 @angular/platform-browser@8.0.0 @angular/platform-browser-dynamic@8.0.0 @angular/platform-server@8.0.0 @angular/router@8.0.0
```

可选:

```
npm install @angular/cdk@8.0.0 @angular/material@8.0.0
```

## 角度偏差依赖性

```
npm install @angular-devkit/build-angular@0.800.0 @angular/compiler-cli@8.0.0 @angular/cli@8.0.0 @angular/language-service@8.0.0 --save-dev
```

## 所有其他相关依赖项

```
npm install core-js@3.1.3 zone.js@0.9.1 rxjs@6.5.0
```

## 相关开发依赖项

```
npm install @types/jasmine@latest @types/node@latest codelyzer@latest karma@latest karma-chrome-launcher@latest karma-cli@latest karma-jasmine@latest karma-jasmine-html-reporter@latest jasmine-core@latest jasmine-spec-reporter@latest protractor@latest tslint@latest webpack@latest rxjs-tslint@latest --save-dev
```

## 以打字打的文件

您应该已经安装了 3.3+版本，TypeScript 3.1 和 3.2 不再受支持。通过将以下内容添加到您的`package.json`的底部，将 TypeScript 添加为对等依赖项。

```
"peerDependencies": {
  "typescript": "3.4.5" 
}
```

如果这里出现错误，请移除`package-lock.json`和`node_modules`并运行`npm install`。

# 重要注意事项

## 惰性装载

有一种新的惰性加载方式:

```
// Before
loadChildren: './path/lazy.module#LazyModule'// Now
loadChildren: () => import('./path/lazy.module').then(m => m.LazyModule)
```

## Tsconfig 和 tslint

在您的`tsconfig.json`中，您应该更改以下`compilerOptions`:

*   将`module`值设为`esnext`
*   将`target`值设为`es2015`

各种`tslint`选项已更新名称——参见[此提交](https://github.com/jeroenouw/AngularMaterialFirebase/commit/94cf25ae7711e5074d1fad4ec6065ef16788f8bc)了解必要的更改。

## 多填充物

在您的`polyfills.ts`中，您需要打开的唯一必要线路是:

```
import 'zone.js/dist/zone';
```

## 静态查询迁移

> 要求所有@ViewChild 和@ContentChild 查询都有一个“static”标志，指定查询是“静态”还是“动态”。编译器以前会自动对查询进行排序，但在 8.0 版本中，开发人员需要明确指定需要哪种行为。这是迁移过程中的一项临时要求”

[更多详情在此](https://angular.io/guide/static-query-migration)。示例:

```
// Before
@ViewChild(MatSort) sort: MatSort;// Now
@ViewChild(MatSort, {static: true}) sort: MatSort;
```

# 可选注释

## 材料设计

> “与其从`@angular/material`导入，不如从具体组件深度导入。如`@angular/material/button`”

## 常春藤

为了试验`ivy`编译器，在`tsconfig.json`中添加以下内容:

```
"angularCompilerOptions": {
   "enableIvy": true
}
```

## 角度变化记录

在这里可以找到[的变化和弃用。](https://github.com/angular/angular/blob/master/CHANGELOG.md#800-2019-05-28)

感谢阅读！