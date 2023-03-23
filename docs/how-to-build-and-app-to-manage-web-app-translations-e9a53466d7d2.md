# 如何构建管理 Web 应用翻译的应用程序—第 1 部分

> 原文：<https://betterprogramming.pub/how-to-build-and-app-to-manage-web-app-translations-e9a53466d7d2>

## 消除网络应用翻译的痛苦

![](img/74b5db0ad5d1e4e96084b6f001327f65.png)

由[卡洛斯·穆扎](https://unsplash.com/@kmuza?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/web?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

管理 web 应用程序的翻译通常是乏味或痛苦的。你必须找到应用程序中的所有文本，获得翻译，然后将条目转换成键值格式，这样你的应用程序才能理解它们。翻译文本是一项缓慢、乏味但又不可避免的工作。然而，一旦以应用程序可以读取的格式输入，管理和转换它们就很容易了。有一些付费的应用程序来做这件事——但是自己免费做这件事很简单！

在这篇文章中，我将构建一个应用程序，它为您的应用程序所需的所有语言提供 JSON 翻译，然后允许用户将它们转换为 YAML 或 CSV，反之亦然。它还允许您将您的 CSV 翻译合并成一个大的 CSV，以便您可以轻松地管理它们。我们允许人们为多个应用程序添加翻译。

# 前端

为此，我们需要为我们的应用程序构建一个前端和后端。前端将用有棱角的材料建造，使建造一个好看的，功能性的用户界面变得容易。后端将使用 Node.js 构建，前端应用将具有接近[https://medium . com/@ ho hanga/how-to-make-a-simple-front-end-with-authenticated-routes-35380 AE 61388](https://medium.com/@hohanga/how-to-make-a-simple-front-end-with-authenticated-routes-35380ae61388)的用户登录、注册和管理功能，因此本故事将跳过一些细节。

要开始构建 Angular 应用程序，我们需要安装 Angular CLI。为此，运行`npm i -g @angular/cli`。然后我们需要通过为我们的项目创建一个文件夹并运行`ng new frontend`来编写应用程序的样板文件。如果有人询问，请务必包括路由模块和 SCSS。之后，我们安装一些库。我们需要安装 Lodash 以方便对象操作，Ngx-Monaco-Editor 以方便富文本编辑器，一个名为 Flat 的包将所有对象的嵌套键展平到一个级别，以便我们可以将它们存储在 CSV 的一列中，Angular JWT 用于管理我们的 auth token，Ngrx Store 用于将 flux store 添加到我们的应用程序以方便状态管理，Angular Material 使我们的应用程序看起来更有吸引力。我们通过运行`npm i @angular/cdk @angular/material @auth0/angular-jwt flat lodah ngx-monaco-editor @ngrx/store`来做到这一点。

然后我们运行`ng add @ngrx/store`来添加使用 Ngrx store 的样板文件。

在`angular.json`中，我们把这段代码:

```
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "version": 1,
  "newProjectRoot": "projects",
  "projects": {
    "frontend": {
      "projectType": "application",
      "schematics": {
        "[@schematics/angular](http://twitter.com/schematics/angular):component": {
          "style": "scss"
        }
      },
      "root": "",
      "sourceRoot": "src",
      "prefix": "app",
      "architect": {
        "build": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:browser",
          "options": {
            "outputPath": "dist/frontend",
            "index": "src/index.html",
            "main": "src/main.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.app.json",
            "aot": false,
            "assets": [
              "src/favicon.ico",
              "src/assets",
              {
                "glob": "**/*",
                "input": "node_modules/ngx-monaco-editor/assets/monaco",
                "output": "./assets/monaco/"
              }
            ],
            "styles": [
              "src/styles.scss"
            ],
            "scripts": []
          },
          "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ],
              "optimization": true,
              "outputHashing": "all",
              "sourceMap": false,
              "extractCss": true,
              "namedChunks": false,
              "aot": true,
              "extractLicenses": true,
              "vendorChunk": false,
              "buildOptimizer": true,
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "2mb",
                  "maximumError": "5mb"
                }
              ]
            }
          }
        },
        "serve": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:dev-server",
          "options": {
            "browserTarget": "frontend:build"
          },
          "configurations": {
            "production": {
              "browserTarget": "frontend:build:production"
            }
          }
        },
        "extract-i18n": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:extract-i18n",
          "options": {
            "browserTarget": "frontend:build"
          }
        },
        "test": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:karma",
          "options": {
            "main": "src/test.ts",
            "polyfills": "src/polyfills.ts",
            "tsConfig": "tsconfig.spec.json",
            "karmaConfig": "karma.conf.js",
            "assets": [
              "src/favicon.ico",
              "src/assets",
              {
                "glob": "**/*",
                "input": "node_modules/ngx-monaco-editor/assets/monaco",
                "output": "./assets/monaco/"
              }
            ],
            "styles": [
              "src/styles.scss"
            ],
            "scripts": []
          }
        },
        "lint": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:tslint",
          "options": {
            "tsConfig": [
              "tsconfig.app.json",
              "tsconfig.spec.json",
              "e2e/tsconfig.json"
            ],
            "exclude": [
              "**/node_modules/**"
            ]
          }
        },
        "e2e": {
          "builder": "[@angular](http://twitter.com/angular)-devkit/build-angular:protractor",
          "options": {
            "protractorConfig": "e2e/protractor.conf.js",
            "devServerTarget": "frontend:serve"
          },
          "configurations": {
            "production": {
              "devServerTarget": "frontend:serve:production"
            }
          }
        }
      }
    }
  },
  "defaultProject": "frontend"
}
```

这确保了文本编辑器将会出现。最重要的部分是:

```
"assets": [
   "src/favicon.ico",
   "src/assets",
   {
      "glob": "**/*",
      "input": "node_modules/ngx-monaco-editor/assets/monaco",
      "output": "./assets/monaco/"
   }
]
```

这将为我们的编辑器加载资产。

安装好所有库后，我们现在可以开始构建应用程序了。我们首先通过运行以下代码来构建我们的共享代码:

```
ng g service user
ng g service object
ng g service translation
ng g service app
ng g guard isAuthenticated
ng g class httpReqInterceptor
ng g class translation
```

这些服务用于对后端的 HTTP 调用。防护是为了防止未经授权的用户访问受保护的路由，HTTP 拦截器允许我们将令牌附加到经过身份验证的路由上。我们应该生成`user.service.ts`、`translation.service.ts`、`object.service.ts`、`app.service.ts`、`is-authenticated.guard.ts`、`translation.ts` 和`http-req-interceptor.ts`。

在`user.service.ts`中，我们把:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { HttpClient } from '[@angular/common](http://twitter.com/angular/common)/http';
import { environment } from 'src/environments/environment';
import { Router } from '[@angular/router](http://twitter.com/angular/router)';
import { JwtHelperService } from "[@auth0/angular-jwt](http://twitter.com/auth0/angular-jwt)";const helper = new JwtHelperService();[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class UserService { constructor(
    private http: HttpClient,
    private router: Router
  ) { }signUp(data) {
    return this.http.post(`${environment.apiUrl}/user/signup`, data);
  }updateUser(data) {
    return this.http.put(`${environment.apiUrl}/user/updateUser`, data);
  } updatePassword(data) {
    return this.http.put(`${environment.apiUrl}/user/updatePassword`, data);
  } login(data) {
    return this.http.post(`${environment.apiUrl}/user/login`, data);
  } logOut() {
    localStorage.clear();
    this.router.navigate(['/']);
  } passwordResetRequest(data) {
    return this.http.post(`${environment.apiUrl}/user/passwordResetRequest`, data);
  } passwordReset(data) {
    return this.http.post(`${environment.apiUrl}/user/passwordReset`, data);
  } isAuthenticated() {
    try {
      const token = localStorage.getItem('token');
      const decodedToken = helper.decodeToken(token);
      const isExpired = helper.isTokenExpired(token);
      return !!decodedToken && !isExpired;
    }
    catch (ex) {
      return false;
    }
  } currentUser() {
    return this.http.get(`${environment.apiUrl}/user/currentUser`);
  }}
```

这将调用我们的路由来登录、注册、更改或重置密码，我们将在后面使用。`isAuthenticated`功能用于确定您的认证令牌是否已经过期或无效。

在`translation.ts`中，我们把:

```
export class Translation {
    public id: number;
    public appId: number;
    public language: string;
    public yaml: string;
    public json: string;
    public csv: string;
}
```

这是我们翻译的样板课。后端将具有相同的列名。

在`translation.service.ts`中，我们输入:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { HttpClient } from '[@angular/common](http://twitter.com/angular/common)/http';
import { Translation } from './translation';
import { environment } from 'src/environments/environment';[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class TranslationService {constructor(
  private http: HttpClient
  ) { } getTranslationsByAppId(appId: number) {
    return this.http.get(`${environment.apiUrl}/translation/translations/${appId}`);
  } addTranslation(translationData: Translation) {
    return this.http.post(`${environment.apiUrl}/translation/add`, translationData);
  } editTranslation(translationData: Translation) {
    return this.http.put(`${environment.apiUrl}/translation/update/${translationData.id}`, translationData);
  } remove(translationId: number) {
    return this.http.delete(`${environment.apiUrl}/translation/delete/${translationId}`);
  }
}
```

这是为了调用我们的路由来更改翻译，可以是 JSON、CSV 或 YAML 格式。

在`app.service.ts`中，我们输入:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { HttpClient } from '[@angular/common](http://twitter.com/angular/common)/http';
import { environment } from 'src/environments/environment';[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class AppService {constructor(
    private http: HttpClient
  ) { } getApps() {
    return this.http.get(`${environment.apiUrl}/app/get`);
  } getApp(id: number) {
    return this.http.get(`${environment.apiUrl}/app/get/${id}`);
  } addApp(appData) {
    return this.http.post(`${environment.apiUrl}/app/add`, appData);
  } editApp(appData) {
    return this.http.put(`${environment.apiUrl}/app/update/${appData.id}`, appData);
  } removeApp(appId) {
    return this.http.delete(`${environment.apiUrl}/app/delete/${appId}`);
  } combineIntoOneCsv(appData) {
    return this.http.put(`${environment.apiUrl}/app/combineCsv/${appData.appId}`, appData);
  }
}
```

这将允许我们对 App 表进行 CRUD 操作，该表包含属于某个用户的应用程序。

在`http-req-interceptor.ts`中，我们输入:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import {
    HttpEvent,
    HttpInterceptor,
    HttpHandler,
    HttpResponse,
    HttpErrorResponse,
    HttpRequest
} from '[@angular/common](http://twitter.com/angular/common)/http';
import { Observable } from 'rxjs';
import { environment } from '../environments/environment'
import { map, filter, tap } from 'rxjs/operators';
import { Router } from '[@angular/router](http://twitter.com/angular/router)';[@Injectable](http://twitter.com/Injectable)()
export class HttpReqInterceptor implements HttpInterceptor {
  constructor(
        public router: Router
    ) { } intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        let modifiedReq = req.clone({}); if (localStorage.getItem('token')) {
            modifiedReq = modifiedReq.clone({
                setHeaders: {
                    authorization: localStorage.getItem('token')
                }
            });
     } return next.handle(modifiedReq).pipe(tap((event: HttpEvent<any>) => {
            if (event instanceof HttpResponse) { }
        }, (err: any) => { }));
  }
}
```

这允许我们附加一个认证令牌来访问我们所有请求的认证路由。

在`is-authenticated.guard.ts`中，我们输入:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot, UrlTree, Router } from '[@angular/router](http://twitter.com/angular/router)';
import { Observable } from 'rxjs';
import { UserService } from './user.service';[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class IsAuthenticatedGuard implements CanActivate {
  constructor(
    private userService: UserService,
    private router: Router
  ) { } canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    const isAuthenticated = this.userService.isAuthenticated();
    if (!isAuthenticated) {
      localStorage.clear();
      this.router.navigate(['/']);
    }
    return isAuthenticated;
  }}
```

这使我们能够阻止未经身份验证的用户，即没有有效身份验证令牌的用户。如果用户未被授权，用户将被发送到主页。

在`object.service.ts`中，我们输入:

```
import { Injectable } from '[@angular/core](http://twitter.com/angular/core)';
import { Translation } from './translation';
declare const require: any;
declare const YAML: any;
const flatten = require('flat');
const unflatten = require('flat').unflatten;
const _ = require('lodash');[@Injectable](http://twitter.com/Injectable)({
  providedIn: 'root'
})
export class ObjectService {constructor() { } convertJsonToYaml(json: string): string {
    return YAML.stringify(JSON.parse(json));
  } convertYamlToJson(yaml: string): string {
    const obj = YAML.parse(yaml);
    return JSON.stringify(obj);
  } convertJsonToCsv(json: string): string {
    let csvString = '';
    const flattenedObj = flatten(JSON.parse(json));
    Object.keys(flattenedObj).forEach(key => {
      csvString += `${key},${flattenedObj[key]}\n`;
    })
    return csvString;
  } convertCsvToJson(csv: string): string {
    let obj = {};
    csv
      .split('\n')
      .forEach(row => {
        const splitRow = row.split(',');
        const key = splitRow[0];
        const value = splitRow[1];
        row[key] = value;
      })
    return JSON.stringify(obj);
  } combineIntoOneCsv(translations: Translation[]) {
    const languages = translations.map(t => t.language);
    const flattenedKeys = Object.keys(flatten(JSON.parse(translations[0].json)));
    let csv = `${['', ...languages].join(',')}\n`;
    flattenedKeys.forEach(fk => {
      let row = [fk];
      languages.forEach(l => {
        const jsonObj = JSON.parse(translations.find(t => t.language == l).json);
        const translation = _.get(jsonObj, fk);
        row.push(translation);
      })
      csv += `${row.join(',')}\n`;
    })
    return csv;
  }
}
```

这项服务完成我们所有的翻译格式转换。对于转换为 CSV，使用`flat`节点包将所有嵌套的关键点展平到一个级别。例如，如果您有对象:

```
{
  foo: {
    bar: 'text"
  }
}
```

然后`foo.bar`会转换成`"foo.bar"`字符串作为键。返回的对象将是:

```
{
  'foo.bar': 'text'
}
```

这使得 JSON 到 CSV 的转换很容易。

要将 YAML 转换成 CSV，我们首先要转换成 JSON，这样我们就可以使用`flat`包。

JSON 和 CSV 之间的转换是通过`yamljs` JavaScript 库完成的，该库位于[https://cdnjs . cloud flare . com/Ajax/libs/YAML js/0 . 3 . 0/YAML . min . js](https://cdnjs.cloudflare.com/ajax/libs/yamljs/0.3.0/yaml.min.js)。下载文件并将其放在`assets`文件夹中，这样我们就可以在`index.html`中引用它。

在运行`ng add @ngrx/store`得到的`reducers`文件夹中，我们创建了`apps-reducer.ts`、`menu-reducer.ts`和`translations-reducer.ts`。

在`app-reducer.ts`中，我们把这个:

```
const SET_APPS = 'SET_APPS';function appsReducer(state, action) {
    switch (action.type) {
        case SET_APPS:
            state = action.payload;
            return state;
        default:
            return state
    }
}export { appsReducer, SET_APPS };
```

在`menu-reducer.ts`中，我们把:

```
const TOGGLE_MENU = 'TOGGLE_MENU';function menuReducer(state, action) {
    switch (action.type) {
        case TOGGLE_MENU:
            state = action.payload;
            return state;
        default:
            return state
    }
}export { menuReducer, TOGGLE_MENU };
```

在`translation-reducer.ts`中，我们把:

```
const SET_TRANSLATIONS = 'SET_TRANSLATIONS';function translationsReducer(state, action) {
    switch (action.type) {
        case SET_TRANSLATIONS:
            state = action.payload;
            return state;
        default:
            return state
    }
}export { translationsReducer, SET_TRANSLATIONS };
```

在我们的`reducers`文件夹的`index.ts`中，也是通过运行`ng add @ngrx/store`得到的，我们放入:

```
import { menuReducer } from './menu-reducer';
import { appsReducer } from './apps-reducer';
import { translationsReducer } from './translations-reducer';export const reducers = {
  menu: menuReducer,
  translations: translationsReducer,
  apps: appsReducer
};
```

来自`@ngrx/store`的`StoreModule`将使用这些文件来创建我们的通量库。

接下来，我们创建组件。他们有很多人。我们运行以下命令来快速创建它们:

```
ng g component addAppDialog
ng g component addTranslationDialog
ng g component AppsPage
ng g component CoversionPage
ng g component EditAppDialog
ng g component HomePage
ng g component LoginPage
ng g component PasswordResetPage
ng g component PasswordResetRequestPage
ng g component SettingsPage
ng g component SignUpPage
ng g component TopBar
```

在`add-app-dialog.ts`中，我们输入:

```
import { Component, OnInit, Inject } from '[@angular/core](http://twitter.com/angular/core)';
import { MatDialogRef, MAT_DIALOG_DATA } from '[@angular/material](http://twitter.com/angular/material)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { AppService } from '../app.service';
import { Store, select } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { SET_APPS } from '../reducers/apps-reducer';[@Component](http://twitter.com/Component)({
  selector: 'app-add-app-dialog',
  templateUrl: './add-app-dialog.component.html',
  styleUrls: ['./add-app-dialog.component.scss']
})
export class AddAppDialogComponent implements OnInit {
  appData: any = <any>{};constructor(
    public dialogRef: MatDialogRef<AddAppDialogComponent>,
    [@Inject](http://twitter.com/Inject)(MAT_DIALOG_DATA) public data: any,
    private appService: AppService,
    private store: Store<any>
  ) {
  } ngOnInit() {
  } cancel(): void {
    this.dialogRef.close();
  } save(addAppForm: NgForm) {
    if (addAppForm.invalid) {
      return;
    }
    this.appService.addApp(this.appData)
      .subscribe(res => {
        this.getApps();
        this.dialogRef.close();
      })
  } getApps() {
    this.appService.getApps()
      .subscribe(res => {
        this.store.dispatch({ type: SET_APPS, payload: res });
      })
  }
}
```

这将由应用程序页面打开，这是用户输入应用程序数据的地方。

在`add-app-dialog.component.html`中，我们把:

```
<h2>Add App</h2>
<form #addAppForm='ngForm' (ngSubmit)='save(addAppForm)'>
    <mat-form-field>
        <input matInput placeholder="Name" required #name='ngModel' name='name' [(ngModel)]='appData.name'>
        <mat-error *ngIf="name.invalid && (name.dirty || name.touched)">
            <div *ngIf="name.errors.required">
                Name is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <textarea matInput placeholder="Description" #description='ngModel' name='description'
            [(ngModel)]='appData.description'>
            </textarea>
    </mat-form-field>
    <br>
    <div mat-dialog-actions>
        <button mat-button (click)="cancel()">Cancel</button>
        <button mat-button cdkFocusInitial>Add</button>
    </div>
</form>
```

这是用户输入应用程序数据的表单。

在`add-app-dialog.component.scss`中，我们把这个:

```
form {
  mat-form-field {
    width: 95%;
    margin: 0 auto;
  }
}
```

这将改变表单域的宽度以适应对话框。

接下来在`add-translation-dialog.component.ts`中，我们添加了以下内容:

```
import { Component, OnInit, Inject } from '[@angular/core](http://twitter.com/angular/core)';
import { MatDialogRef, MAT_DIALOG_DATA } from '[@angular/material](http://twitter.com/angular/material)';
import { TranslationService } from '../translation.service';
import { Store } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { Translation } from '../translation';
import { SET_TRANSLATIONS } from '../reducers/translations-reducer';
import { ObjectService } from '../object.service';[@Component](http://twitter.com/Component)({
  selector: 'app-add-translation-dialog',
  templateUrl: './add-translation-dialog.component.html',
  styleUrls: ['./add-translation-dialog.component.scss']
})
export class AddTranslationDialogComponent implements OnInit {
  translationData: Translation = <Translation>{};
  jsonEditorOptions = { language: 'javascript', theme: 'vs-dark' };
  yamlEditorOptions = { language: 'yaml', theme: 'vs-dark' };
  csvEditorOptions = { theme: 'vs-dark' }; constructor(
    public dialogRef: MatDialogRef<AddTranslationDialogComponent>,
    [@Inject](http://twitter.com/Inject)(MAT_DIALOG_DATA) public appId: number,
    private translationService: TranslationService,
    private store: Store<any>,
    private objectService: ObjectService
  ) { }ngOnInit() {
  } cancel(): void {
    this.dialogRef.close();
  } save(addTranslationForm: NgForm) {
    if (addTranslationForm.invalid) {
      return;
    }
    let translationData = Object.assign({}, this.translationData);
    translationData.appId = this.appId;
    this.translationService.addTranslation(translationData)
      .subscribe(res => {
        this.getTranslations();
        this.dialogRef.close();
      })
  } getTranslations() {
    this.translationService.getTranslationsByAppId(this.appId)
      .subscribe(res => {
        this.store.dispatch({ type: SET_TRANSLATIONS, payload: res });
      })
  } convertJsonToYaml() {
    const yaml = this.objectService.convertJsonToYaml(this.translationData.json);
    this.translationData.yaml = yaml;
  } convertYamlToJson() {
    const json = this.objectService.convertYamlToJson(this.translationData.yaml);
    this.translationData.json = json;
  } convertJsonToCsv() {
    const csv = this.objectService.convertJsonToCsv(this.translationData.json);
    this.translationData.csv = csv;
  } convertCsvToJson() {
    const json = this.objectService.convertCsvToJson(this.translationData.csv);
    this.translationData.json = json;
  } convertYamlToCsv() {
    const json = this.objectService.convertYamlToJson(this.translationData.yaml);
    const csv = this.objectService.convertJsonToCsv(json);
    this.translationData.csv = csv;
  } convertCsvToYaml() {
    const json = this.objectService.convertCsvToJson(this.translationData.csv);
    const yaml = this.objectService.convertJsonToYaml(json);
    this.translationData.yaml = yaml;
  }
}
```

在这个对话框中，用户可以添加 JSON、CSV 和 YAML 格式的译文。

在`add-translation-dialog.component.html`中，我们这样写道:

```
<form #addTranslationForm='ngForm' (ngSubmit)='save(addTranslationForm)'>
    <h1 class="center">Add Language</h1>
    <div id='language'>
        <mat-form-field>
            <input matInput placeholder="Language" required #language='ngModel' name='language'
                [(ngModel)]='translationData.language'>
            <mat-error *ngIf="language.invalid && (language.dirty || language.touched)">
                <div *ngIf="language.errors.required">
                    Language is required.
                </div>
            </mat-error>
        </mat-form-field>
    </div>
    <br><h2 class="center">JSON</h2>
    <ngx-monaco-editor [options]="jsonEditorOptions" name='json' [(ngModel)]="translationData.json"></ngx-monaco-editor>
    <div class="center">
        <button mat-raised-button type='button' (click)='convertJsonToYaml()'>JSON to YAML</button>
        <button mat-raised-button type='button' (click)='convertJsonToCsv()'>JSON to CSV</button>
    </div><h2 class="center">YAML</h2>
    <ngx-monaco-editor [options]="yamlEditorOptions" name='yaml' [(ngModel)]="translationData.yaml"></ngx-monaco-editor>
    <div class="center">
        <button mat-raised-button type='button' (click)='convertYamlToJson()'>YAML to JSON</button>
        <button mat-raised-button type='button' (click)='convertYamlToCsv()'>YAML to CSV</button>
    </div><h2 class="center">CSV</h2>
    <ngx-monaco-editor [options]="csvEditorOptions" name='csv' [(ngModel)]="translationData.csv"></ngx-monaco-editor>
    <div class="center">
        <button mat-raised-button type='button' (click)='convertCsvToJson()'>CSV to JSON</button>
        <button mat-raised-button type='button' (click)='convertCsvToYaml()'>CSV to YAML</button>
    </div>
    <div mat-dialog-actions>
        <button mat-button (click)="cancel()">Cancel</button>
        <button mat-button cdkFocusInitial type='submit'>Add Translations</button>
    </div>
</form>
```

这意味着用户可以输入他们的翻译数据。

在`add-translation-dialog.component.scss`中，我们把:

```
form {
  mat-form-field {
    width: 95%;
    margin: 0 auto;
  }
}#language {
  width: 100%;
  margin: 0 auto;
}
```

这确保了表单域适合对话框。

接下来，在`apps-page.component.ts`中，我们把这个:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { AppService } from '../app.service';
import { MatDialog } from '[@angular/material](http://twitter.com/angular/material)';
import { AddAppDialogComponent } from '../add-app-dialog/add-app-dialog.component';
import { EditAppDialogComponent } from '../edit-app-dialog/edit-app-dialog.component';
import { Store, select } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { SET_APPS } from '../reducers/apps-reducer';[@Component](http://twitter.com/Component)({
  selector: 'app-apps-page',
  templateUrl: './apps-page.component.html',
  styleUrls: ['./apps-page.component.scss']
})
export class AppsPageComponent implements OnInit {
  apps: any[] = []; constructor(
    private appService: AppService,
    public dialog: MatDialog,
    private store: Store<any>
  ) {
    store.pipe(select('apps'))
      .subscribe(apps => {
        this.apps = (apps || [])
          .filter(a => !a.deleted)
          .sort((a, b) => +a.id - +b.id);
      })
  } ngOnInit() {
    this.getApps();
  } getApps() {
    this.appService.getApps()
      .subscribe(res => {
        this.apps = res as any[];
        this.store.dispatch({ type: SET_APPS, payload: res });
      })
  } openAddAppDialog() {
    const dialogRef = this.dialog.open(AddAppDialogComponent, {
      width: '70vw',
      data: {}
    }) dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
    });
  } openEditAppDialog(index: number) {
    const dialogRef = this.dialog.open(EditAppDialogComponent, {
      width: '70vw',
      data: Object.assign({}, this.apps[index])
    }) dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
    });
  } deleteApp(index: number) {
    this.appService.removeApp(this.apps[index].id)
      .subscribe(res => {
        this.getApps();
      })
  }
}
```

该组件的代码获取属于用户的所有应用程序并显示它们，用按钮打开对话框以添加和编辑应用程序数据并删除应用程序。

在`apps-page.component.html`中，我们有这个:

```
<div class="center">
    <h1>Apps</h1>
</div>
<div>
    <h2>Add App</h2>
    <button mat-raised-button (click)='openAddAppDialog()'>Add App</button>
</div>
<div>
    <h2>Apps List</h2>
    <mat-card *ngFor='let a of apps; let i = index'>
        <mat-card-title>
            {{a.name}}
        </mat-card-title>
        <mat-card-subtitle>
            {{a.description}}
        </mat-card-subtitle>
        <mat-card-actions>
            <a mat-button routerLink='/convert/{{a.id}}'>Change Translations</a>
            <button mat-button (click)='openEditAppDialog(i)'>Edit</button>
            <button mat-button (click)='deleteApp(i)'>Delete</button>
        </mat-card-actions>
    </mat-card></div>
```

顶部有标题和一个打开添加应用程序对话框的按钮。底部有显示应用程序及其相应的编辑和删除按钮的卡片，以及一个链接到用户可以输入应用程序翻译的页面。

在`conversion-page.component.ts`中，我们把:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { ObjectService } from '../object.service';
import { Translation } from '../translation';
import { MatDialog } from '[@angular/material](http://twitter.com/angular/material)';
import { AddTranslationDialogComponent } from '../add-translation-dialog/add-translation-dialog.component';
import { ActivatedRoute } from '[@angular/router](http://twitter.com/angular/router)';
import { Store, select } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { TranslationService } from '../translation.service';
import { SET_TRANSLATIONS } from '../reducers/translations-reducer';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { AppService } from '../app.service';[@Component](http://twitter.com/Component)({
  selector: 'app-conversion-page',
  templateUrl: './conversion-page.component.html',
  styleUrls: ['./conversion-page.component.scss']
})
export class ConversionPageComponent implements OnInit {
  jsonEditorOptions = { language: 'javascript' };
  yamlEditorOptions = { language: 'yaml' };
  csvEditorOptions = {};
  appTranslations: Translation[] = [];
  appId: number;
  appData: any = <any>{};constructor(
    private objectService: ObjectService,
    public dialog: MatDialog,
    private activeRoute: ActivatedRoute,
    private store: Store<Translation>,
    private translationService: TranslationService,
    private appService: AppService
  ) {
    this.activeRoute.params.subscribe(params => {
      this.appId = params.appId;
    }) store.pipe(select('translations'))
      .subscribe(appTranslations => {
        this.appTranslations = (appTranslations || [])})
  } ngOnInit() {
    this.getTranslations();
    this.getApp();
  } getTranslations() {
    this.translationService.getTranslationsByAppId(this.appId)
      .subscribe(res => {
        this.store.dispatch({ type: SET_TRANSLATIONS, payload: res });
      })
  } openAddTranslationDialog() {
    const dialogRef = this.dialog.open(AddTranslationDialogComponent, {
      width: '70vw',
      maxHeight: '90vh',
      data: this.appId
    })dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
    });
  } convertJsonToYaml(index: number) {
    const yaml = this.objectService.convertJsonToYaml(this.appTranslations[index].json);
    this.appTranslations[index].yaml = yaml;
  } convertYamlToJson(index: number) {
    const json = this.objectService.convertYamlToJson(this.appTranslations[index].yaml);
    this.appTranslations[index].json = json;
  } convertJsonToCsv(index: number) {
    const csv = this.objectService.convertJsonToCsv(this.appTranslations[index].json);
    this.appTranslations[index].csv = csv;
  } convertCsvToJson(index: number) {
    const json = this.objectService.convertCsvToJson(this.appTranslations[index].csv);
    this.appTranslations[index].json = json;
  } convertYamlToCsv(index: number) {
    const json = this.objectService.convertYamlToJson(this.appTranslations[index].yaml);
    const csv = this.objectService.convertJsonToCsv(json);
    this.appTranslations[index].csv = csv;
  } convertCsvToYaml(index: number) {
    const json = this.objectService.convertCsvToJson(this.appTranslations[index].csv);
    const yaml = this.objectService.convertJsonToYaml(json);
    this.appTranslations[index].yaml = yaml;
  } editTranslation(editTranslationForm: NgForm, translation: Translation) {
    if (editTranslationForm.invalid) {
      return;
    }
    this.translationService.editTranslation(translation)
      .subscribe(res => {
        this.getTranslations();
      })
  } combineIntoOneCsv() {
    const combinedCsv = this.objectService.combineIntoOneCsv(this.appTranslations);
    this.appService.combineIntoOneCsv({ combinedCsv, appId: this.appId })
      .subscribe(res => {
        this.getApp();
      })
  } getApp() {
    this.appService.getApp(this.appId)
      .subscribe(res => {
        this.appData = res;
      })
  }
}
```

我们获得用户应用程序的所有翻译并显示出来。此外，我们允许用户将所有翻译的 CSV 文件合并成一个文件。他们还可以一键转换格式之间的翻译。

在`conversion-page.component.ts`中，我们有:

```
<div class="center">
    <h1>Convert Translation Formats</h1>
    <button mat-raised-button (click)='openAddTranslationDialog()'>Add Language</button>
    <button mat-raised-button (click)='combineIntoOneCsv()'>Combine CSVs into One</button>
</div>
<br>
<mat-card>
    <mat-card-title>
        <div class="language">
            Combined CSV
        </div>
    </mat-card-title>
    <mat-card-content>
        <ngx-monaco-editor [options]="csvEditorOptions" name='combinedCsv' [(ngModel)]="appData.combinedCsv">
        </ngx-monaco-editor>
    </mat-card-content>
</mat-card><mat-card *ngFor='let a of appTranslations; let i = index'>
    <mat-card-title>
        <div class="language">
            {{a.language}}
        </div>
    </mat-card-title>
    <mat-card-content>
        <form #editTranslationForm='ngForm'>
            <div class="language">
                <mat-form-field>
                    <input matInput placeholder="Language" required #language='ngModel' name='language'
                        [(ngModel)]='a.language'>
                    <mat-error *ngIf="language.invalid && (language.dirty || language.touched)">
                        <div *ngIf="language.errors.required">
                            Language is required.
                        </div>
                    </mat-error>
                </mat-form-field>
            </div>
            <br><h2 class="center">JSON</h2>
            <ngx-monaco-editor [options]="jsonEditorOptions" name='json' [(ngModel)]="a.json"></ngx-monaco-editor>
            <div class="center">
                <button mat-raised-button type='button' (click)='convertJsonToYaml(i)'>JSON to YAML</button>
                <button mat-raised-button type='button' (click)='convertJsonToCsv(i)'>JSON to CSV</button>
            </div><h2 class="center">YAML</h2>
            <ngx-monaco-editor [options]="yamlEditorOptions" name='yaml' [(ngModel)]="a.yaml"></ngx-monaco-editor>
            <div class="center">
                <button mat-raised-button type='button' (click)='convertYamlToJson(i)'>YAML to JSON</button>
                <button mat-raised-button type='button' (click)='convertYamlToCsv(i)'>YAML to CSV</button>
            </div><h2 class="center">CSV</h2>
            <ngx-monaco-editor [options]="csvEditorOptions" name='csv' [(ngModel)]="a.csv"></ngx-monaco-editor>
            <div class="center">
                <button mat-raised-button type='button' (click)='convertCsvToJson(i)'>CSV to JSON</button>
                <button mat-raised-button type='button' (click)='convertCsvToYaml(i)'>CSV to YAML</button>
            </div>
            <mat-card-actions>
                <button mat-raised-button type='button' (click)='editTranslation(editTranslationForm, a)'>Edit
                    Language</button>
                <button mat-raised-button type='button'>Delete Language</button>
            </mat-card-actions>
        </form>
    </mat-card-content>
</mat-card>
```

这将向用户显示用于输入翻译和语言名称的表单，以及用于编辑和删除翻译的按钮。

在`conversion-page.component.scss`中，我们把:

```
form {
  mat-form-field {
    width: 95%;
    margin: 0 auto;
  }
}.language {
  width: 95%;
  margin: 0 auto;
}
```

这将调整我们的表单域组件的宽度。

接下来在`edit-app-dialog.component.ts`中，我们输入:

```
import { Component, OnInit, Inject } from '[@angular/core](http://twitter.com/angular/core)';
import { MatDialogRef, MAT_DIALOG_DATA } from '[@angular/material](http://twitter.com/angular/material)';
import { AppService } from '../app.service';
import { Store } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { SET_APPS } from '../reducers/apps-reducer';[@Component](http://twitter.com/Component)({
  selector: 'app-edit-app-dialog',
  templateUrl: './edit-app-dialog.component.html',
  styleUrls: ['./edit-app-dialog.component.scss']
})
export class EditAppDialogComponent implements OnInit { constructor(
    public dialogRef: MatDialogRef<EditAppDialogComponent>,
    [@Inject](http://twitter.com/Inject)(MAT_DIALOG_DATA) public appData: any,
    private appService: AppService,
    private store: Store<any>
  ) { } ngOnInit() {
  } save(addAppForm: NgForm) {
    if (addAppForm.invalid) {
      return;
    }
    this.appService.editApp(this.appData)
      .subscribe(res => {
        this.getApps();
        this.dialogRef.close();
      })
  } getApps() {
    this.appService.getApps()
      .subscribe(res => {
        this.store.dispatch({ type: SET_APPS, payload: res });
      })
  }
}
```

这允许用户编辑和保存他们的应用程序数据。然后在`edit-app-dialog.component.ts`，我们有:

```
<h2>Edit App</h2>
<form #editAppForm='ngForm' (ngSubmit)='save(editAppForm)'>
    <mat-form-field>
        <input matInput placeholder="Name" required #name='ngModel' name='name' [(ngModel)]='appData.name'>
        <mat-error *ngIf="name.invalid && (name.dirty || name.touched)">
            <div *ngIf="name.errors.required">
                Name is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <textarea matInput placeholder="Description" #description='ngModel' name='description'
            [(ngModel)]='appData.description'>
            </textarea>
    </mat-form-field>
    <br>
    <div mat-dialog-actions>
        <button mat-button (click)="cancel()">Cancel</button>
        <button mat-button cdkFocusInitial>Edit</button>
    </div>
</form>
```

这允许用户编辑他们的应用程序的数据。

然后在`edit-app-dialog.component.scss`中，我们放入:

```
form {
  mat-form-field {
    width: 95%;
    margin: 0 auto;
  }
}
```

这将调整表单域的宽度。

在`home-page.component.ts`中，我们输入:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { ObjectService } from '../object.service';
declare const require: any;
const flatten = require('flat')[@Component](http://twitter.com/Component)({
  selector: 'app-home-page',
  templateUrl: './home-page.component.html',
  styleUrls: ['./home-page.component.scss']
})
export class HomePageComponent implements OnInit {constructor(
  ) { }ngOnInit() {}}
```

因为我们在主页上没有任何功能。

在`home-page.component.html`中，我们放入:

```
<div class="center">
    <h1>Translation Manager</h1>
</div>
```

这显示了我们的应用程序的标题。

在`login-page.component.ts`中，我们把:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { UserService } from '../user.service';
import { Router } from '[@angular/router](http://twitter.com/angular/router)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';[@Component](http://twitter.com/Component)({
  selector: 'app-login-page',
  templateUrl: './login-page.component.html',
  styleUrls: ['./login-page.component.scss']
})
export class LoginPageComponent implements OnInit {
  loginData: any = <any>{}; constructor(
    private userService: UserService,
    private router: Router
  ) { } ngOnInit() {
  } login(loginForm: NgForm) {
    if (loginForm.invalid) {
      return;
    }
    this.userService.login(this.loginData)
      .subscribe((res: any) => {
        localStorage.setItem('token', res.token);
        this.router.navigate(['/apps']);
      }, err => {
        alert('Invalid username or password');
      })
  }
}
```

这使得用户可以通过调用我们的函数进行 POST 请求来登录，发送用户的用户名和密码。

在`home-page.component.html`中，我们把这个:

```
<div class="center">
    <h1>Log In</h1>
</div>
<form #loginForm='ngForm' (ngSubmit)='login(loginForm)'>
    <mat-form-field>
        <input matInput placeholder="Username" required #username='ngModel' name='username'
            [(ngModel)]='loginData.username'>
        <mat-error *ngIf="username.invalid && (username.dirty || username.touched)">
            <div *ngIf="username.errors.required">
                Username is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <input matInput placeholder="Password" type='password' required #password='ngModel' name='password'
            [(ngModel)]='loginData.password'>
        <mat-error *ngIf="password.invalid && (password.dirty || password.touched)">
            <div *ngIf="password.errors.required">
                Password is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Log In</button>
    <a mat-raised-button routerLink='/passwordResetRequest'>Reset Password</a>
</form>
```

这是登录表单。

在`password-reset-page.component.ts`中，我们把:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { UserService } from '../user.service';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { ActivatedRoute } from '[@angular/router](http://twitter.com/angular/router)';[@Component](http://twitter.com/Component)({
  selector: 'app-password-reset-page',
  templateUrl: './password-reset-page.component.html',
  styleUrls: ['./password-reset-page.component.scss']
})
export class PasswordResetPageComponent implements OnInit {
  passwordResetData: any = <any>{};
  passwordResetToken: string;
  constructor(
    private userService: UserService,
    private route: ActivatedRoute
  ) {
    route.queryParams.subscribe(params => {
      this.passwordResetToken = params['passwordResetToken'];
    })
  } ngOnInit() {
  } passwordReset(passwordResetForm: NgForm) {
    if (passwordResetForm.invalid ||
      this.passwordResetData.confirmPassword
      != this.passwordResetData.password) {
      return;
    }
    let data = Object.assign({}, this.passwordResetData);
    data.passwordResetToken = this.passwordResetToken;
    this.userService.passwordReset(data)
      .subscribe(res => {
        alert('Password reset successful');
      }, err => {
        alert('Invalid Password Reset Token');
      })
  }
}
```

这将发送 HTTP 密码重置请求。

在`password-reset-page.component.html`中，我们把这个:

```
<div class="center">
    <h1>Reset Password</h1>
</div>
<form #passwordResetForm='ngForm' (ngSubmit)='passwordReset(passwordResetForm)'>
    <mat-form-field>
        <input type='password' matInput placeholder="Password" required #password='ngModel' name='password'
            [(ngModel)]='passwordResetData.password'>
        <mat-error *ngIf="password.invalid && (password.dirty || password.touched)">
            <div *ngIf="password.errors.required">
                Password is required.
            </div>>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <input type='password' matInput placeholder="Confirm Password" required #confirmPassword='ngModel'
            name='confirmPassword' [(ngModel)]='passwordResetData.confirmPassword'>
        <mat-error *ngIf="confirmPassword.invalid &&
        (confirmPassword.dirty ||
        confirmPassword.touched  ||
        passwordResetData.confirmPassword != passwordResetData.password)">
            <div *ngIf="confirmPassword.errors.required">
                Confirm Password is required.
            </div>
            <div *ngIf="passwordResetData.confirmPassword != passwordResetData.password">
                Password and Confirm Password must match.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Reset Password</button>
</form>
```

用户可以输入他们的密码和确认密码，如果匹配，密码将被提交。

在`password-reset-request-page.component.ts`中，我们输入:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { UserService } from '../user.service';[@Component](http://twitter.com/Component)({
  selector: 'app-password-reset-request-page',
  templateUrl: './password-reset-request-page.component.html',
  styleUrls: ['./password-reset-request-page.component.scss']
})
export class PasswordResetRequestPageComponent implements OnInit {
  passwordResetData: any = <any>{}; constructor(
    private userService: UserService
  ) { } ngOnInit() {
  } passwordResetRequest(passwordResetRequestForm: NgForm) {
    if (passwordResetRequestForm.invalid) {
      return;
    }
    this.userService.passwordResetRequest(this.passwordResetData)
      .subscribe(res => {
        alert('Password reset email sent');
      }, err => {
        alert('Email not found');
      })
  }
}
```

用户可以发送他们的密码重置请求，收到一封带有密码重置令牌和密码重置说明的电子邮件。

在`password-reset-request-page.component.html`中，我们输入:

```
<div class="center">
    <h1>Request Password Reset Email</h1>
</div>
<form #passwordResetRequestForm='ngForm' (ngSubmit)='passwordResetRequest(passwordResetRequestForm)'>
    <mat-form-field>
        <input pattern="\S+@\S+\.\S+" matInput placeholder="Email" required #email='ngModel' name='email'
            [(ngModel)]='passwordResetData.email'>
        <mat-error *ngIf="email.invalid && (email.dirty || email.touched)">
            <div *ngIf="email.errors.required">
                Email is required.
            </div>
            <div *ngIf="email.invalid">
                Email is invalid.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Get Password Reset Email</button>
</form>
```

如果在用户表中找到电子邮件，用户可以输入他们的电子邮件以获得密码重置电子邮件。

在`settings-page.component.ts`中，我们输入:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { ActivatedRoute, Router } from '[@angular/router](http://twitter.com/angular/router)';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { UserService } from '../user.service';[@Component](http://twitter.com/Component)({
  selector: 'app-settings-page',
  templateUrl: './settings-page.component.html',
  styleUrls: ['./settings-page.component.scss']
})
export class SettingsPageComponent implements OnInit {
  currentTwitterUser: any = <any>{};
  elements: any[] = [];
  displayedColumns: string[] = ['key', 'value'];
  updateUserData: any = <any>{};
  updatePasswordData: any = <any>{};constructor(
    private userService: UserService,
  ) { } ngOnInit() {
    this.currentUser();
  } updateUser(updateUserForm: NgForm) {
    if (updateUserForm.invalid) {
      return;
    }
    this.userService.updateUser(this.updateUserData)
      .subscribe(res => {
        alert('Updated user info successful.');
      }, err => {
        alert('Updated user info failed.');
      })
  } updatePassword(updatePasswordForm: NgForm) {
    if (updatePasswordForm.invalid) {
      return;
    }
    this.userService.updatePassword(this.updatePasswordData)
      .subscribe(res => {
        alert('Updated password successful.');
      }, err => {
        alert('Updated password failed.');
      })
  } currentUser() {
    this.userService.currentUser()
      .subscribe(res => {
        this.updateUserData = res;
      }, err => { })
  }
}
```

用户可以更改他们的用户名、电子邮件和密码。

在`settings-page.component.html`中，我们输入:

```
<div class="center">
    <h1>Settings</h1>
</div>
<br>
<div>
    <h2>Update User Info</h2>
</div>
<br>
<form #updateUserForm='ngForm' (ngSubmit)='updateUser(updateUserForm)'>
    <mat-form-field>
        <input matInput placeholder="Username" required #userName='ngModel' name='userName'
            [(ngModel)]='updateUserData.userName'>
        <mat-error *ngIf="userName.invalid && (userName.dirty || userName.touched)">
            <div *ngIf="userName.errors.required">
                Username is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <input pattern="\S+@\S+\.\S+" matInput placeholder="Email" required #email='ngModel' name='email'
            [(ngModel)]='updateUserData.email'>
        <mat-error *ngIf="email.invalid && (email.dirty || email.touched)">
            <div *ngIf="email.errors.required">
                Email is required.
            </div>
            <div *ngIf="email.invalid">
                Email is invalid.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Update User Info</button>
</form>
<br><div>
    <h2>Update Password</h2>
</div>
<br>
<form #updatePasswordForm='ngForm' (ngSubmit)='updatePassword(updatePasswordForm)'>
    <mat-form-field>
        <input matInput placeholder="Password" type='password' required #password='ngModel' name='password'
            [(ngModel)]='updatePasswordData.password'>
        <mat-error *ngIf="password.invalid && (password.dirty || password.touched)">
            <div *ngIf="password.errors.required">
                Password is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Update Password</button>
</form>
```

人们可以输入并保存他们的用户数据。

在`sign-up-page.component.ts`中，我们输入:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { UserService } from '../user.service';
import { NgForm } from '[@angular/forms](http://twitter.com/angular/forms)';
import { Router } from '[@angular/router](http://twitter.com/angular/router)';
import _ from 'lodash';[@Component](http://twitter.com/Component)({
  selector: 'app-sign-up-page',
  templateUrl: './sign-up-page.component.html',
  styleUrls: ['./sign-up-page.component.scss']
})
export class SignUpPageComponent implements OnInit {
  signUpData: any = <any>{}; constructor(
    private userService: UserService,
    private router: Router
  ) { } ngOnInit() {
  } signUp(signUpForm: NgForm) {
    if (signUpForm.invalid) {
      return;
    }
    this.userService.signUp(this.signUpData)
      .subscribe(res => {
        this.login();
      }, err => {
        console.log(err);
        if (
          _.has(err, 'error.error.errors') &&
          Array.isArray(err.error.error.errors) &&
          err.error.error.errors.length > 0
        ) {
          alert(err.error.error.errors[0].message);
        }
      })
  } login() {
    this.userService.login(this.signUpData)
      .subscribe((res: any) => {
        localStorage.setItem('token', res.token);
        this.router.navigate(['/apps']);
      })
  }
}
```

用户可以注册并在注册后立即登录。为了简化工作流程，我们在示例中跳过了用户验证。

在`sign-up-page.component.html`中，我们把:

```
<div class="center">
    <h1>Sign Up</h1>
</div>
<br>
<form #signUpForm='ngForm' (ngSubmit)='signUp(signUpForm)'>
    <mat-form-field>
        <input matInput placeholder="Username" required #username='ngModel' name='username'
            [(ngModel)]='signUpData.username'>
        <mat-error *ngIf="username.invalid && (username.dirty || username.touched)">
            <div *ngIf="username.errors.required">
                Username is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <input pattern="\S+@\S+\.\S+" matInput placeholder="Email" required #email='ngModel' name='email'
            [(ngModel)]='signUpData.email'>
        <mat-error *ngIf="email.invalid && (email.dirty || email.touched)">
            <div *ngIf="email.errors.required">
                Email is required.
            </div>
            <div *ngIf="email.invalid">
                Email is invalid.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <mat-form-field>
        <input matInput placeholder="Password" type='password' required #password='ngModel' name='password'
            [(ngModel)]='signUpData.password'>
        <mat-error *ngIf="password.invalid && (password.dirty || password.touched)">
            <div *ngIf="password.errors.required">
                Password is required.
            </div>
        </mat-error>
    </mat-form-field>
    <br>
    <button mat-raised-button type='submit'>Sign Up</button>
</form>
```

用户可以通过输入用户名和密码注册。

在`top-bar.component.ts`中，我们放入:

```
import { Component, OnInit } from '[@angular/core](http://twitter.com/angular/core)';
import { Store, select } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { TOGGLE_MENU } from '../reducers/menu-reducer';[@Component](http://twitter.com/Component)({
  selector: 'app-top-bar',
  templateUrl: './top-bar.component.html',
  styleUrls: ['./top-bar.component.scss']
})
export class TopBarComponent implements OnInit {
  menuOpen: boolean; constructor(
    private store: Store<any>
  ) {
    store.pipe(select('menu'))
      .subscribe(menuOpen => {
        this.menuOpen = menuOpen;
      })
  } ngOnInit() {
  } toggleMenu() {
    this.store.dispatch({ type: TOGGLE_MENU, payload: !this.menuOpen    });
  }
}
```

这是 elts 通过更新商店中的菜单状态来切换菜单的逻辑。

在`top-bar.component.html`中，我们把:

```
<mat-toolbar>
    <a (click)='toggleMenu()' class="menu-button">
        <i class="material-icons">
            menu
        </i>
    </a>
    Translation Manager
</mat-toolbar>
```

用户会看到顶栏。在`top-bar.component.scss`中，我们输入:

```
.menu-button {
    margin-top: 6px;
    margin-right: 10px;
    cursor: pointer;
}
```

这给出了菜单图标和文本之间的间距。

在`app-routing.module.ts`，我们有:

```
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';
import { Routes, RouterModule } from '[@angular/router](http://twitter.com/angular/router)';
import { HomePageComponent } from './home-page/home-page.component';
import { AppsPageComponent } from './apps-page/apps-page.component';
import { ConversionPageComponent } from './conversion-page/conversion-page.component';
import { LoginPageComponent } from './login-page/login-page.component';
import { SignUpPageComponent } from './sign-up-page/sign-up-page.component';
import { SettingsPageComponent } from './settings-page/settings-page.component';
import { PasswordResetRequestPageComponent } from './password-reset-request-page/password-reset-request-page.component';
import { PasswordResetPageComponent } from './password-reset-page/password-reset-page.component';
import { IsAuthenticatedGuard } from './is-authenticated.guard';const routes: Routes = [
  { path: '', component: HomePageComponent },
  { path: 'apps', component: AppsPageComponent },
  { path: 'convert/:appId', component: ConversionPageComponent },
  { path: 'login', component: LoginPageComponent },
  { path: 'signup', component: SignUpPageComponent },
  { path: 'settings', component: SettingsPageComponent, canActivate: [IsAuthenticatedGuard] },
  { path: 'passwordResetRequest', component: PasswordResetRequestPageComponent },
  { path: 'passwordReset', component: PasswordResetPageComponent },
];[@NgModule](http://twitter.com/NgModule)({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

这意味着 URL 被映射到组件。用户可以通过输入列出的相应 URL 来访问这些页面。

在`app.component.html`中，我们输入:

```
<mat-sidenav-container class="example-container">
    <mat-sidenav mode="side" [opened]='menuOpen'>
        <ul>
            <li>
                <b>
                    Translation Manager
                </b>
            </li>
            <li>
                <a routerLink='/login' *ngIf='!isAuthenticated()'>Log In</a>
            </li>
            <li>
                <a routerLink='/signup' *ngIf='!isAuthenticated()'>Sign Up</a>
            </li>
            <li>
                <a href='#' (click)='logOut()' *ngIf='isAuthenticated()'>Log Out</a>
            </li>
            <li>
                <a routerLink='/apps' *ngIf='isAuthenticated()'>Apps</a>
            </li>
            <li>
                <a routerLink='/settings' *ngIf='isAuthenticated()'>Settings</a>
            </li>
        </ul></mat-sidenav>
    <mat-sidenav-content>
        <app-top-bar></app-top-bar>
        <div id='content'>
            <router-outlet></router-outlet>
        </div>
    </mat-sidenav-content>
</mat-sidenav-container>
```

我们可以看到上面文件中列出的菜单、顶栏和路线。

在`app.component.scss`中，我们输入:

```
#content {
    padding: 20px;
    min-height: 100vh;
  }ul {
    list-style-type: none;
    margin: 0;
    li {
      padding: 20px 5px;
    }
}
```

这给了页面和菜单项一些填充。

在`app.component.ts`中，我们放入:

```
import { Component, HostListener } from '[@angular/core](http://twitter.com/angular/core)';
import { Store, select } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { TOGGLE_MENU } from './reducers/menu-reducer';
import { UserService } from './user.service';[@Component](http://twitter.com/Component)({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  menuOpen: boolean; constructor(
    private store: Store<any>,
    private userService: UserService
  ) {
    store.pipe(select('menu'))
      .subscribe(menuOpen => {
        this.menuOpen = menuOpen;
      })
  } isAuthenticated() {
    return this.userService.isAuthenticated();
  } [@HostListener](http://twitter.com/HostListener)('document:click', ['$event'])
  public onClick(event) {
    const isOutside = !event.target.className.includes("menu-button") &&
      !event.target.className.includes("material-icons") &&
      !event.target.className.includes("mat-drawer-inner-container")
    if (isOutside) {
      this.menuOpen = false;
      this.store.dispatch({ type: TOGGLE_MENU, payload: this.menuOpen });
    }
  } logOut() {
    this.userService.logOut();
  }
}
```

这意味着用户可以注销，菜单只在点击菜单按钮时显示，当 URL 改变时消失。

然后在`app.module.ts`中，我们放入:

```
import { BrowserModule } from '[@angular/platform-browser](http://twitter.com/angular/platform-browser)';
import { NgModule } from '[@angular/core](http://twitter.com/angular/core)';
import { MonacoEditorModule } from 'ngx-monaco-editor';
import { BrowserAnimationsModule } from '[@angular/platform-browser](http://twitter.com/angular/platform-browser)/animations';
import {
  MatButtonModule,
  MatCheckboxModule,
  MatInputModule,
  MatMenuModule,
  MatSidenavModule,
  MatToolbarModule,
  MatTableModule,
  MatDialogModule,
  MAT_DIALOG_DEFAULT_OPTIONS,
  MatDatepickerModule,
  MatSelectModule,
  MatCardModule,
  MatFormFieldModule
} from '[@angular/material](http://twitter.com/angular/material)';import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { ObjectService } from './object.service';
import { HomePageComponent } from './home-page/home-page.component';
import { LoginPageComponent } from './login-page/login-page.component';
import { SignUpPageComponent } from './sign-up-page/sign-up-page.component';
import { FormsModule } from '[@angular/forms](http://twitter.com/angular/forms)';
import { ConversionPageComponent } from './conversion-page/conversion-page.component';
import { AppsPageComponent } from './apps-page/apps-page.component';
import { SettingsPageComponent } from './settings-page/settings-page.component';
import { StoreModule } from '[@ngrx/store](http://twitter.com/ngrx/store)';
import { reducers } from './reducers';
import { HttpClientModule, HTTP_INTERCEPTORS } from '[@angular/common](http://twitter.com/angular/common)/http';
import { HttpReqInterceptor } from './http-req-interceptor';
import { UserService } from './user.service';
import { PasswordResetRequestPageComponent } from './password-reset-request-page/password-reset-request-page.component';
import { PasswordResetPageComponent } from './password-reset-page/password-reset-page.component';
import { TopBarComponent } from './top-bar/top-bar.component';
import { TranslationService } from './translation.service';
import { AddAppDialogComponent } from './add-app-dialog/add-app-dialog.component';
import { EditAppDialogComponent } from './edit-app-dialog/edit-app-dialog.component';
import { AddTranslationDialogComponent } from './add-translation-dialog/add-translation-dialog.component';[@NgModule](http://twitter.com/NgModule)({
  declarations: [
    AppComponent,
    HomePageComponent,
    LoginPageComponent,
    SignUpPageComponent,
    ConversionPageComponent,
    AppsPageComponent,
    SettingsPageComponent,
    PasswordResetRequestPageComponent,
    PasswordResetPageComponent,
    TopBarComponent,
    AddAppDialogComponent,
    EditAppDialogComponent,
    AddTranslationDialogComponent,],
  imports: [
    BrowserModule,
    AppRoutingModule,
    FormsModule,
    MonacoEditorModule.forRoot(),
    MatButtonModule,
    StoreModule.forRoot(reducers),
    BrowserAnimationsModule,
    MatButtonModule,
    MatCheckboxModule,
    MatFormFieldModule,
    MatInputModule,
    MatMenuModule,
    MatSidenavModule,
    MatToolbarModule,
    MatTableModule,
    FormsModule,
    HttpClientModule,
    MatDialogModule,
    MatDatepickerModule,
    MatSelectModule,
    MatCardModule
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: HttpReqInterceptor,
      multi: true
    },
    ObjectService,
    UserService,
    TranslationService,
    {
      provide: MAT_DIALOG_DEFAULT_OPTIONS,
      useValue: { hasBackdrop: false }
    },
  ],
  bootstrap: [AppComponent],
  entryComponents: [
    AddAppDialogComponent,
    EditAppDialogComponent,
    AddTranslationDialogComponent
  ],
})
export class AppModule { }
```

这确保了我们需要的所有组件和库都包含在模块中，因此它们可以在我们的应用程序中使用。

在`environment.ts`中，我们把:

```
export const environment = {
  production: false,
  apiUrl: '[http://localhost:8080'](http://localhost:8080')
};
```

这允许我们访问我们的后端应用程序。