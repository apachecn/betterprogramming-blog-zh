# 用 Next.js 和 Firebase 实现用户认证

> 原文：<https://betterprogramming.pub/implement-user-authentication-with-next-js-and-firebase-fb9414adba08>

## Auth 不一定很难或者很耗时

![](img/c0c200480c16522961be57e71184a55a.png)

里卡多·戈麦斯·安吉尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

构建您自己的身份验证系统可能是一项困难且耗时的任务。使用现有平台来处理您的身份验证流程将为您节省大量时间，甚至不需要花费您任何金钱。Firebase 是最受欢迎的平台之一，可以为您处理身份验证，它的免费计划允许任何开发人员轻松快速地构建安全的身份验证系统。它帮助您快速构建应用程序，而无需管理基础架构。在本教程中，我们将使用这个平台来快速实现我们自己的认证流程。我们将使用 Next.js、Typescript 和 TailwindCSS 来实现这一点。

要跟进，你应该有一个现有的项目。您可以遵循以下关于更好编程的其他教程来设置您的项目:

[](https://medium.com/better-programming/how-to-set-up-next-js-with-typescript-5f912766c88) [## 如何用 TypeScript 设置 Next.js

### 使用 Next.js 和 TypeScript 启动新的 React 项目

medium.com](https://medium.com/better-programming/how-to-set-up-next-js-with-typescript-5f912766c88) [](https://medium.com/better-programming/how-to-set-up-next-js-with-tailwind-css-b93ccd2d4164) [## 如何用 Tailwind CSS 设置 Next.js

### 在 React 项目中使用 Tailwind CSS 和 Next.js

medium.com](https://medium.com/better-programming/how-to-set-up-next-js-with-tailwind-css-b93ccd2d4164) [](https://medium.com/better-programming/how-to-use-eslint-and-prettier-in-your-next-js-application-with-typescript-8ffc9ac5b672) [## 如何在带有 TypeScript 的 Next.js 应用程序中使用 ESLint 和 appearlier

### 让每个人都在同一页上，用同一种风格

medium.com](https://medium.com/better-programming/how-to-use-eslint-and-prettier-in-your-next-js-application-with-typescript-8ffc9ac5b672) 

# 概观

在本教程中，我们将实现以下内容:

*   实现一个登录、注册和重置密码的表单。
*   在 Firestore 数据库中存储额外的用户数据，并在用户登录时获取这些数据..
*   创建了一个 useAuth 挂钩，并使用上下文 API 向所有组件提供用户数据。
*   每当我们获取数据时，显示一个加载微调图标。
*   每当出错时显示错误消息。
*   创建了一个钩子，当用户输入一个已验证但未登录时重定向用户。
*   创建一个注销按钮。

# 创建登录和注册页面

让我们从设置我们的认证页面开始，并确保我们可以在我们的应用程序中导航这些页面。

创建文件`/pages/signup.tsx`:

让我们为我们的`Login`页面(`/pages/login.tsx`)做同样的事情，并确保我们可以在两者之间导航。

如果你还没有这样做，用`yarn dev`启动你的服务器，在你的浏览器中导航到`/login`来测试页面是否工作。

# 创建表单

现在我们有了自己的页面，让我们创建实际的注册和登录表单。在 React 中处理表单的一个很好的库是`[react-hook-form](<https://react-hook-form.com/>)`。它帮助您构建高性能、灵活且可扩展的表单，并提供易于使用的验证。让我们先安装它:

```
yarn add react-hook-form
```

现在我们可以创建我们的注册表单:

现在在`pages/signup.tsx`中导入`SignUpForm`组件，并用刚刚创建的`<SignUpForm />`替换“todo”注释。

您可以通过提交表单并在控制台中检查日志来测试表单。它应该会向您显示一个如下所示的对象:

```
{
  name: Jake,
  email: jake@prins.com,
  password: secretpassword,
}
```

此外，尝试验证是否有效。所有字段都应该是必填的。应该验证电子邮件的格式是否正确，并且密码应该至少有 6 个字符长。

我们做得很好，让我们对登录表单做同样的事情。

创建`/components/forms/LoginForm.tsx`，复制并粘贴你的注册表单代码。把名字改成`LoginForm`，去掉“姓名”输入，因为我们登录只需要邮箱和密码。另外，调整`LoginData`界面

您的表单应该如下所示:

# 创建一个 Firebase 项目

现在我们已经准备好了表单，我们需要将它们与 Firebase 集成。让我们从建立一个 Firebase 项目开始。

首先，我们需要创建一个 Firebase 帐户。前往[https://firebase.google.com/](https://firebase.google.com/)，点击“开始”按钮，并按照指示创建您的项目。

一旦创建了项目，您应该在 Firebase 控制台中注册您的应用程序。在项目概述页面中，单击 web 图标将 Firebase 添加到您的 web 应用程序中。创建完成后，您将收到 firebase 配置，看起来应该是这样的:

```
const firebaseConfig = {
    apiKey: "xxxx",
    authDomain: "your-project-name.firebaseapp.com",
    databaseURL: "<https://your-project-name.firebaseio.com>",
    projectId: "your-project-name",
    storageBucket: "your-project-name.appspot.com",
    messagingSenderId: "xxx",
    appId: "xxx",
    measurementId: "G-xxx"
  };
```

我们现在应该激活要添加到应用程序中的注册方法。导航至*认证*，通过激活*电子邮件/密码*方法开始。

## **添加 Firestore(可选)**

如果您想在注册时保存额外的信息，比如用户名或全名，您需要建立一个数据库来保存这些数据。 [Cloud Firestore](https://firebase.google.com/docs/firestore) 是 Firebase 的一款灵活、可扩展的数据库。它提供了与 Firebase 和其他谷歌云平台产品的无缝集成，比如云功能。就像 Firebase 一样，它完全免费。只有当您的应用程序真正开始扩展时，您可能会超过免费计划，但即使这样，您也只需为您使用的内容付费。一个非常有趣的价格模型，如果你刚开始时不想花很多(或任何)钱的话。

如果您想要设置 Firestore，请导航到*数据库*，然后单击第一个*创建数据库*按钮，将云 Firestore 添加到您的项目中。选择在测试模式下启动的选项。我们以后再来担心 Firestore 规则。

# 在您的项目中实现 Firebase

我们现在准备在我们的项目中实现 Firebase。

首先，安装 Firebase:

```
yarn add firebase
```

我们需要将我们的 Firebase 配置保存到一些环境变量中。

Next.js 自带对环境变量的内置支持，允许你[使用](https://nextjs.org/docs/basic-features/environment-variables#loading-environment-variables) `[.env.local](https://nextjs.org/docs/basic-features/environment-variables#loading-environment-variables)` [加载环境变量](https://nextjs.org/docs/basic-features/environment-variables#loading-environment-variables)和[将环境变量暴露给浏览器](https://nextjs.org/docs/basic-features/environment-variables#exposing-environment-variables-to-the-browser)。

转到您的`.env.local`文件(或者创建它，如果它还不存在的话)。然后，在下面的示例中传递，用您自己的 Firebase 凭证更改虚拟数据。

```
NEXT_PUBLIC_FIREBASE_API_KEY="yourapikey"
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN="yourappname.firebaseapp.com"
NEXT_PUBLIC_FIREBASE_DATABASE_URL="<https://appname.firebaseio.com>"
NEXT_PUBLIC_FIREBASE_PROJECT_ID="yourappname"
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET="yourappname.appspot.com"
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID="yoursenderid"
NEXT_PUBLIC_FIREBASE_APP_ID="yourappid"
NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID="yourmeasurementid"
```

只有在低于 9.4 的 Next.js 版本上，你才应该创建一个`next.config.ts`文件并自己导出你的环境变量。

太好了，我们现在可以这样访问我们的数据了:`process.env.NEXT_PUBLIC_FIREBASE_API_KEY`。仔细检查`.env.local`是否被添加到您的`.gitignore`中，这样您就不会意外地提交这些数据。还要记住，在部署应用程序时，首先需要设置生产环境变量。当在 [Vercel](https://vercel.com) 上部署时，您可以在 Vercel 仪表板的项目的[环境变量](https://vercel.com/docs/v2/build-step#environment-variables)部分中配置秘密。

接下来，创建一个文件`/config/firebase.ts`并添加您的配置。

在这个文件中，我们首先声明我们的 Firebase 配置，然后用`firebase.initializeApp(firebaseConfig)`初始化我们项目中的 Firebase。

如您所见，我们还导出了一些在开发应用程序时需要的函数。

# 签约雇用

现在我们已经准备好在我们的项目中使用 Firebase，让我们转到我们的`SignUpForm`组件，开始创建我们的`signUp`函数。

```
...
import { auth } from 'config/firebase';
...const signUp = ({ name, email, password }) => {
 return auth
  .createUserWithEmailAndPassword(email, password)
  .then((response) => {
   console.log(response)
  })
  .catch((error) => {
   return { error };
  });
};
```

现在你可以尝试你的形式:

```
const onSubmit = (data: SignUpData) => {
 return signUp(data).then((user) => {
  console.log(user);
 });
};
```

如果你现在尝试这种形式，你将注册你的 firebase 项目。转到 Firebase 控制台，并转到身份验证概述。您应该在这里看到提交的数据。

太神奇了！仅仅用这几行代码，您就构建了一个注册表单！

太好了，但是 *name* 属性会怎么样呢？嗯，如果我们想存储额外的数据，我们还想在 Firestore 数据库中创建用户。因此，让我们创建一个在数据库中创建用户文档的函数，并确保记录的 UID 是相同的。

```
...
import { auth } from 'config/firebase';
...const createUser = (user) => {
 return db
  .collection('users')
  .doc(user.uid)
  .set(user)
  .then(() => {
   console.log("Success")
  })
  .catch((error) => {
   console.log(error)
  });
};
```

现在，一旦用户成功注册，我们就可以调用这个函数。继续更新您的`signUp`函数:

```
const signUp = ({ name, email, password }) => {
 return auth
  .createUserWithEmailAndPassword(email, password)
  .then((response) => {
   return createUser({ uid: response.user.uid, email, name });
  })
  .catch((error) => {
   return { error };
  });
};
```

如果你现在再试一次，你会得到一个错误信息，因为你不能用同一个电子邮件地址注册两次。确保您已经删除了 [Firebase 控制台](https://console.firebase.google.com/)中的第一个条目，然后重试(或者尝试使用不同的电子邮件)。

提交注册表单后，在 [Firebase 控制台](https://console.firebase.google.com/)中的*认证*和*数据库*中查看是否创建了两条记录。

太好了，你现在应该有一个工作的注册表单了！

## 句柄状态

基于成功响应，我们现在可以在用户注册后将他们重定向到不同的路由，但唯一的问题是我们将丢失我们的状态。在重定向的页面上，我们仍然需要能够判断用户是否登录。我们还希望在应用程序的多个地方使用当前用户的数据。

此时，我们需要考虑全局状态。我们可以使用像 [Redux](https://redux.js.org/) 这样的状态管理库，但是这也引入了很多复杂性，并不总是你想要的。特别是现在 React 有了上下文和钩子，我们在这一点上并不真正需要状态管理库。

# 创建身份验证提供程序

[上下文](https://reactjs.org/docs/context.html)提供了一种通过组件树传递数据的方法，而不必在每一层手动向下传递属性。

让我们从创建我们的`useAuthProvider`钩子开始，将我们的`signUp`和`createUser`函数移到这个钩子中。我们还用`useState`设置成功或出错后的用户状态。

创建一个名为`hooks/useAuth`的文件，并添加以下代码:

现在让我们使用[上下文](https://reactjs.org/docs/context.html)来创建一个提供者。在刚刚放入`hooks/useAuth.tsx`的代码上添加以下代码

很好——现在用这个提供者包装你的`AppComponent`,这样我们就可以在应用程序的任何地方使用这个`useAuth`钩子。在你的`_app.tsx`:

```
import { AppProps } from 'next/app';
import { AuthProvider } from 'hooks/useAuth';
export default function MyApp({ Component, pageProps }: AppProps): any {
 return (
  <AuthProvider>
   <Component {...pageProps} />
  </AuthProvider>
 );
}
```

我们也来做一个如果成功登录可以重定向到的页面:`pages/dashboard.tsx`。

酷，现在让我们来试试吧！如果你想用同一个电子邮件再试一次，那么在你提交表格之前，首先从 Firebase 中删除你的用户数据(或者使用不同的电子邮件)。如果一切正常，您现在应该会被重定向到仪表板页面。

噪音。这看起来越来越像了。让我们利用我们的势头，继续前进。

# 注册

让我们创建我们的登录功能。在`useAuth.tsx`内添加以下功能。

```
const signIn = ({ email, password }) => {
 return auth
  .signInWithEmailAndPassword(email, password)
  .then((response) => {
   setUser(response.user);
   return response.user;
  })
  .catch((error) => {
   return { error };
  });
};
```

确保导出它。

```
...
return { user, signUp, signIn };
...
```

现在在`LoginForm.tsx`中调用提交时函数:

```
...const LoginForm: React.FC = () => {
 const { register, errors, handleSubmit } = useForm();
 const auth = useAuth();
 const router = useRouter();const onSubmit = (data: LoginData) => {
  return auth.signIn(data).then(() => {
   router.push('/dashboard');
  });
 };...
```

酷，现在让我们试试这是否可行。

你现在应该可以登录了，接受我们缺少了*名字*。这是因为该名称不是来自 Firebase 身份验证，而是存储在我们的 Firestore 数据库中。我们需要监听身份验证状态的变化，并从数据库中获取数据。

在`useAuthProvider`钩子内添加这个函数:

```
const getUserAdditionalData = (user: firebase.User) => {
 return db
  .collection('users')
  .doc(user.uid)
  .get()
  .then((userData) => {
   if (userData.data()) {
    setUser(userData.data());
   }
  });
};
```

然后，在成功的登录请求后调用这个函数。

```
const signIn = ({ email, password }) => {
 return auth
  .signInWithEmailAndPassword(email, password)
  .then((response) => {
   setUser(response.user);
   getUserAdditionalData(user);
   return response.user;
  })
  .catch((error) => {
   return { error };
  });
};
```

太好了，现在这应该可以解决我们的问题了。但它并不能解决所有问题，因为 Firebase 还能帮助我们保持用户登录。但是在用户登录后，当他们重新进入我们的应用程序时，我们也需要获取额外的数据。

因此，让我们也订阅`onAuthStateChanged`事件，并调用`getUserAdditionalData`来获取数据:

```
const handleAuthStateChanged = (user: firebase.User) => {
 setUser(user);
 if (user) {
  getUserAdditionalData(user);
 }
};useEffect(() => {
 const unsub = auth.onAuthStateChanged(handleAuthStateChanged);

 return () => unsub();
}, []);
```

如果你刷新页面，你应该看到你的名字。神奇！🎩

为了进一步改进这一点，我们还可以确保每当用户的文档更新时，我们也更新应用程序中的用户状态。我们可以通过向 useAuth 钩子添加另一个效果来做到这一点，该钩子订阅用户的文档并在它改变时更新状态。

```
useEffect(() => {
  if (user?.uid) {
    // Subscribe to user document on mount
    const unsubscribe = db
      .collection('users')
      .doc(user.uid)
      .onSnapshot((doc) => setUser(doc.data()));return () => unsubscribe();
  }
}, []);
```

# 注销

我们现在确保从我们的数据库中获取用户数据，并更新我们的本地状态，以便用户知道他或她已经登录。这很好，但是我们还没有完成。我们还要确保用户可以注销。

将此`signOut`功能添加到`useAuthProvider`中:

```
...const signOut = () => {
  return auth.signOut().then(() => setUser(false));
};...
```

我们希望能够在任何地方调用这个函数，所以不要忘记导出它。

```
...
return { user, signUp, signIn, signOut };
...
```

现在，我们要做的就是做一个按钮，当它被点击时调用这个函数。将此按钮添加到您的`pages/dashboard.tsx`:

```
...
<button
 onClick={() => auth.signOut()}
 className="w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-500 focus:outline-none focus:border-indigo-700 focus:shadow-outline-indigo active:bg-indigo-700 transition duration-150 ease-in-out"
>
 Sign out
</button>
```

试试吧！

如果工作正常，你将会看到一个白屏。这不是我们真正想要的。我们实际上从来不希望用户在没有登录的情况下能够进入`/dashboard`，所以让我们创建一个钩子，如果用户没有登录，它将重定向用户。

在`hooks/useRequireAuth.tsx`处创建以下钩子

```
import { useEffect } from 'react';
import { useRouter } from 'next/router';
import { useAuth } from 'hooks/useAuth';export const useRequireAuth = () => {
 const auth = useAuth();
 const router = useRouter();useEffect(() => {
  if (auth.user === false) {
   router.push('/login');
  }
 }, [auth, router]);

 return auth;
};
```

现在使用`dashboard.tsx`里面的这个钩子。用我们的新吊钩替换旧的`const auth = useAuth()`:

```
...
const auth = useRequireAuth()
...
```

又来了，魔法！🎩

你做得太棒了。我们建立了一个工作注册和登录流程。不错！去喝杯咖啡吧，这是你应得的。当你回来的时候，我们还需要再写一些代码。

# 重置密码

因为当用户忘记密码时会发生什么？我们也需要处理这个问题，所以让我们给我们的`useAuth`钩子添加一个函数来发送一个重置密码链接。

```
const sendPasswordResetEmail = (email) => {
  return auth.sendPasswordResetEmail(email).then((response) => {
   return response;
  });
 };...return {
 user,
 signUp,
 signIn,
 signOut,
 sendPasswordResetEmail,
};
```

现在创建一个`ResetPasswordForm`组件，用户可以在其中输入他们的电子邮件。在提交时，它应该调用新`sendPasswordResetEmail`函数。

太好了。现在创建显示我们新的`ResetPasswordForm`组件的`reset-password`页面。

此外，在我们的登录表单上添加一个到这个新的`reset-password`页面的链接。将此代码添加到密码输入的正下方:

```
...<div className="mt-4 flex items-end">
 <div className="text-sm leading-5">
  <Link href="/reset-password">
   <a
    href="#"
    className="font-medium text-indigo-600 hover:text-indigo-500 focus:outline-none focus:underline transition ease-in-out duration-150"
   >
    Forgot your password?
   </a>
  </Link>
 </div>
</div>...
```

很好。现在测试一下，看看您全新的认证流程！

这里总是有一些改进的空间。让我们来处理加载状态和错误消息。

# 加载状态和错误消息

我们现在有了一个有效的认证流程，这很好，但仍有很大的改进空间。让我们从处理加载状态开始，并确保在出错时显示错误消息。

在您的`SignInForm`组件中，导入`useState`并使用它来设置`isLoading`和`error`的状态。当我们提交表单时，我们将`isLoading`设置为`true`并清除错误。响应返回后，我们将`isLoading`设置回`false`，只有当出现错误时，我们才设置错误。

```
import { useState } from 'react';const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState(null);const onSubmit = (data: LoginData) => {
 setIsLoading(true);
 setError(null);
 return auth.signIn(data).then((response) => {
  setIsLoading(false);
  response.error ? setError(response.error) : router.push('/dashboard');
 });
};
```

如果存在错误，我们希望在表单中显示出来:

```
{error?.message && (
 <div className="mb-4 text-red-500 text-center border-dashed border border-red-600 p-2 rounded">
  <span>{error.message}</span>
 </div>
)}
```

我们也喜欢向用户显示我们正在获取数据，所以让我们在`isLoading`为`true`时更改提交按钮的文本。

因为我们在所有的表单上都使用这个按钮，所以让我们创建一个可重用的按钮组件，我们可以在不同的地方导入它。

```
interface ButtonProps {
 title?: string;
 isLoading?: boolean;
}const Button = ({
 isLoading,
 title,
 children,
 ...buttonProps
}: ButtonProps &
 React.ButtonHTMLAttributes<HTMLButtonElement>): JSX.Element => {
 return (
  <button
   className="w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-500 focus:outline-none focus:border-indigo-700 focus:shadow-outline-indigo active:bg-indigo-700 transition duration-150 ease-in-out"
   {...buttonProps}
  >
   {isLoading ? 'Loading...' : title}
   {children}
  </button>
 );
};export default Button;
```

现在，我们可以像这样在表单组件中使用按钮:

```
import Button from 'components/elements/Button';...<Button title="Login" type="submit" isLoading={isLoading} />...
```

那好多了。我们可以通过显示一个微调图标来代替“加载…”文本，这样会更好。

在`icons/spinner.tsx`，创建微调图标组件:

```
const Spinner = (props: React.SVGProps<SVGSVGElement>): JSX.Element => {
 return (
  <svg viewBox="0 0 100 100" {...props}>
   <path d="M90.9 58.2c-3.8 18.9-20.1 32.7-39.7 33-22 .4-40.7-17-41.9-39C8 28.5 26.9 8.8 50.4 8.8c19 0 35.5 13.1 40 31.2.3 1.2 1.4 2.1 2.7 2.1 1.8 0 3.1-1.7 2.7-3.5C90.6 18.1 72 3.3 50.4 3.3c-27.2 0-49 23.4-46.6 51.1 2.1 23 21 41.2 44 42.4C71.6 98 91.7 81.9 96.2 59.4c.3-1.7-1-3.3-2.7-3.3-1.3-.1-2.4.8-2.6 2.1z" />
  </svg>
 );
};export default Spinner;
```

现在，在我们的`Button`组件中，用微调组件替换加载文本:

```
{isLoading ? (
 <Spinner width="20" fill="white" className="animate-spin" />
) : (
 title
)}
```

注意:确保你的版本至少是 1.7，这样你就可以使用`animate-spin`类。

噪音。现在，继续为注册和重置密码表单做同样的事情，今天到此为止。

# 摘要

*   我们为身份验证表单实现了不同的页面。
*   我们已经实现了登录、注册和重置密码表单。
*   我们在 Firestore 数据库中存储额外的用户数据，并在用户登录时获取这些数据。
*   我们实现了一个注销按钮。
*   我们已经创建了一个 useAuth 挂钩，并使用上下文 API 向所有组件提供用户数据。
*   每当我们获取数据时，我们显示一个加载微调图标。
*   每当出现问题时，我们都会显示错误消息。
*   我们已经创建了一个钩子，当用户输入一个经过身份验证但没有登录时，它可以重定向用户。

就是这样！感谢阅读。我希望它有帮助。祝你项目的下一步顺利。