# 如何用笑话实现观众的嘲笑

> 原文：<https://betterprogramming.pub/what-i-learned-about-mocking-with-spectator-tests-e36ecf2a426c>

## 我从旁观者测试中学到了什么

![](img/3a2ae43c1fff5c72a7b824c55e523fbb.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我在我之前的[博客文章](https://javascript.plainenglish.io/how-to-use-spectator-to-test-your-angular-components-a125c95cf24b)中提到,《旁观者》目前是我 Angular 应用程序的首选测试工具。在过去的几周里，我学到了一些关于使用旁观者嘲讽的东西，希望对你有用。

请注意，我使用的是带有玩笑意味的旁观者。

# 删除未使用的提供程序

在我的`createServiceFactory`或`createComponentFactory`中，我已经声明我的依赖项将被自动嘲笑，但我仍然保留了带有`useValue: {}`的提供者。在下面的例子中，我的`UserApiService`已经被自动嘲讽了。在这种情况下，不再需要在带有`useValue: {}`的提供者中声明`UserApiService`:

```
let spectator: SpectatorService<UserService>;
  const createService = createServiceFactory({
    service: UserService,
    providers: [
      { provide: UserApiService, useValue: {}}, // TODO: Remove, we don't need it.
    ],
    mocks: [UserApiService], // Automatically mock
  });

  beforeEach(() => (spectator = createService()));
```

我们不需要供应商中的`UserApiService`:

```
let spectator: SpectatorService<UserService>;
  const createService = createServiceFactory({
    service: UserService,
    mocks: [UserApiService],
  });

  beforeEach(() => (spectator = createService()));
```

将`UserApiService`保留在提供者中没有任何副作用。不过，不用的话最好去掉。

# Andre return()vs mock return value()

自从我开始使用旁观者以来，我一直专门使用`andReturn()`来模拟测试用例中的方法。最近才知道 Jest 的`mockReturnValue()`。其工作方式与`andReturn()`相同。我注意到的主要区别是`andReturn()`对其返回类型并不严格。

给定一个返回类型`Observable<User>`的方法。

```
class UserService {
  getUser(): Observable<User> {
    // code here..    
  }
}
```

其中`User`为:

```
interface User {
  firstName: string;
  lastName: string;
}
```

使用`mockReturnValue`，如果我没有使用预期的返回类型进行模拟，我将得到一个错误:

```
const userUservice = spectator.inject(UserService);
userService.getUser.mockReturnValue(of('user mock'));
```

错误:

```
error TS2345: Argument of type 'Observable<string>' 
is not assignable to parameter of type 'Observable<User>'.
```

`mockReturnValue()`只接受方法声明的返回类型:

```
const userUservice = spectator.inject(UserService);
userService.getUser.mockReturnValue(of({firstName: 'First', lastName: 'Last'} as User));
```

使用`andReturn()`，我可以使用不同的类型:

```
const userUservice = spectator.inject(UserService);
userService.getUser.andReturn(of('user mock')); // I can mock with a string type!
```

# 通过直接赋值进行模拟

如果我关心 mock 的返回值，我已经在测试用例中使用了`andReturn()`(不久将使用`mockReturnValue()`)。当我在寻找一个测试中出现的模拟时，我发现更容易发现。

然而，在一些测试案例中，我只对断言一个被模仿的方法的`toHaveBeenCalled()`感兴趣。在这种情况下，我通常会将`jest.fn()`赋给我想要断言的方法。这仅在属性或方法是*而不是*只读时才有效。

如果我在组件中使用`UserService`。

```
constructor(private userService: UserService) {}
```

我可以通过直接分配模拟函数在测试中模拟它的`getUser()`方法调用。然后用`toHaveBeenCalled()`断言:

```
spectator.component['userService'].getUser = jest.fn();
//.. some code here
expect(spectator.component['userService'].getUser).toHaveBeenCalled();
```

# 模拟只读属性

如果我想模仿的方法是只读的怎么办？

我有一个助手:

```
export class UserService {
  //.. some code
  get canAccess$(): Observable<boolean> {
    // .. implementaion
  }
}
```

我有一个组件使用了上面的 getter `UserService.canAccess$`。当我试图用`andReturn()`模仿 getter 时:

```
const userService = spectator.inject(UserService);
userService.canAccess$.andReturnvalue(of(false));
```

我得到以下错误:

```
TS2339: Property 'andReturn' does not exist on type 'Observable '.
```

我也不能直接分配 mock，因为这里的`canAccess$`是只读的:

```
spectator.component['userService'].canAccess$ = of(false);Cannot assign to 'canAccess$' because it is a read-only property.
```

有几种方法可以解决这个问题。

## 使用 Object.defineProperty()

我之前使用了`Object.defineProperty`来修改服务对象的属性:

```
Object.defineProperty(spectator.component['userService'], 'canAccess$', { value: of(true) });
```

这奏效了。我认为一定有一种方法可以通过使用旁观者而不是直接修改服务对象来实现这一点，见下一节。

## 在测试用例中设置 useValue

我是在浏览《观察家》自述文件中的[例子](https://github.com/ngneat/spectator#mocking-constructor-dependencies)时发现这种方法的。

我可以通过设置`useValue`在我的`createComponentFactory`调用中为`canAccess$`声明一个默认的模拟。

```
const createComponent = createComponentFactory({
  component: MyComponent,
  //...typeOrOptions here
  providers: [
    { provide: UserService, useValue: { canAccess$: of(true) } }
  ],
});
```

套件中的所有测试都将使用这个默认值`canAccess$`，除非我在一个测试或另一个测试套件中覆盖它。为了覆盖默认的模拟，我可以在测试用例中用`useValue`指定提供者。在这个例子中，我将返回值改为`of(false)`。

```
const provider = {
  provide: UserService,
  useValue: { canAccess: of(false) },
};
```

然后使用我刚刚声明的带有模拟覆盖的提供者调用`createComponent()`。

```
spectator = createComponent({
  providers: [provider],
});
```

我可以在单独的测试用例中覆盖默认的模拟。

```
it('should prevent access...', () => {
  const provider = {
    provide: UserService,
    useValue: { canAccess$: of(false) },
  };
  spectator = createComponent({
    providers: [provider],
  });
  //.. code here
});
```

或者，如果我想在测试套件中使用它，我可以在`beforeEach()`中声明覆盖:

```
describe('Prevent access', () => {
  beforeEach(() => {
    const provider = {
      provide: UserService,
      useValue: { canAccess$: of(false) },
    };
    spectator = createComponent({
      providers: [provider],
    });
    //.. code here
  });
  it('should prevent access...', () => {
    // .. code here
  });
  // .. more tests
});
```

如果你喜欢这个故事，你可能也会喜欢我关于旁观者测试和 Angular 的其他故事:

[](https://javascript.plainenglish.io/how-to-use-spectator-to-test-your-angular-components-a125c95cf24b) [## 如何使用旁观者来测试你的角组件

### 使用示例应用程序的操作指南。

javascript.plainenglish.io](https://javascript.plainenglish.io/how-to-use-spectator-to-test-your-angular-components-a125c95cf24b) [](/unsubscribing-multiple-observables-in-angular-46c9c4833ffc) [## 如何在 Angular 中自动退订多个可观测量

### 让我们使用异步管道和 RxJS 操作符的力量

better 编程. pub](/unsubscribing-multiple-observables-in-angular-46c9c4833ffc) [](https://blog.ardy.me/membership) [## 通过我的推荐链接加入媒体

### 阅读 Ardy Gallego Dedase(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接…

blog.ardy.me](https://blog.ardy.me/membership)