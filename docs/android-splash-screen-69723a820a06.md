# 构建一个现代的 Android 闪屏

> 原文：<https://betterprogramming.pub/android-splash-screen-69723a820a06>

## 使用最新 API 的方法

![](img/99fdad6425a61d585c113e84d39f112d.png)

闪屏剖析

Android 发布官方支持显示闪屏。

这种新的闪屏支持是在 Android 12 中添加的，但同样可以在早期版本的 Android 中使用[闪屏支持库](https://androidx.tech/artifacts/core/core-splashscreen/)。

为了演示闪屏 API 的各个方面，我用 Jetpack Compose 创建了一个[示例应用](https://github.com/sridhar-sp/android-splash-screen-demo)([最终闪屏预览](https://raw.githubusercontent.com/sridhar-sp/android-splash-screen-demo/main/docs/images/splash_preview.png))。

我们开始吧！

# 第一步

让我们从添加闪屏[依赖关系](https://androidx.tech/artifacts/core/core-splashscreen/)开始。

```
implementation 'androidx.core:core-splashscreen:1.0.0-alpha02'
```

# 第二步

接下来的步骤是创建一个闪屏图标和一个可选的品牌形象。

我们将在后面讨论如何创建闪屏图标，并使它出现在屏幕的中心——在屏幕底部的`Extras`部分。

# 第三步

一旦我们准备好图像资产，我们就可以使用闪屏主题来应用它。

```
<style name="Theme.Splash.Starting" parent="Theme.SplashScreen">
    <item name="windowSplashScreenBackground">
        @color/splash_screen_background
    </item>
    <item name="windowSplashScreenAnimatedIcon">
        @drawable/ic_splash_icon
    </item>
    <item name="windowSplashScreenAnimationDuration">
        @integer/splash_screen_animation_duration
    </item>
    <item name="postSplashScreenTheme">
        @style/Theme.App.NoActionBar
    </item>
    <!--Android 12 specific styles (put this in values-v31) -->
    <item name="android:windowSplashScreenBrandingImage">
        @drawable/ic_brand_icon
    </item>
    <!--Android 12 specific styles (put this in values-v31) -->
</style>
```

![](img/3e4d91dc74a8482717557d8771f0c3c5.png)

闪屏剖析

我们来看看风格属性。

`windowSplashScreenBackground` —你可能已经猜到了，占据整个屏幕的是背景色。

`windowSplashScreenAnimatedIcon` —这可以是一个普通的矢量图标，也可以是一个动画矢量图标。注意:一个动画矢量图标将只在 Android 12+设备上显示动画，旧的 API 设备将只显示静态图标而没有任何动画。

`windowSplashScreenAnimationDuration` —动画图标的持续时间。建议的持续时间为 1000 毫秒。

`postSplashScreenTheme` —该主题在应用程序加载后使用。
这是必需的，因为我们将在应用程序清单中将启动器活动主题设置为`Theme.SplashScreen`——这对于显示闪屏、动画徽标和品牌图标非常有用。然而，一旦应用程序被加载，如果我们不使用`Theme.AppCompat`或`Theme.MaterialComponents`，我们会得到一个异常。因此，这个属性来拯救。

`windowSplashScreenBrandingImage` —显示在屏幕底部的品牌图像(仅适用于 Android 12)。

# 第四步

现在设置启动器活动主题为`**Theme.Splash.Starting**`

```
<activity
    android:name=".MainActivity"
    android:exported="true"
    android:label="@string/app_name"
    android:theme="@style/Theme.Splash.Starting">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

# 第五步

最后在 MainActivity `onCreate`方法中添加`***installSplashScreen()***`

```
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    installSplashScreen()

    setContent {
        AndroidSplashScreenTheme {
             Greeting("Android splash screen demo")
        }
    }
}
```

# 其他功能

既然我们已经完成了新 Android 闪屏的设置，现在我们可以看看它附带的其他功能了。

## 控制闪屏可见性。

闪屏 API 提供支持，只要我们需要，闪屏就保持可见。

假设我们需要获取一些对渲染屏幕至关重要的资源，没有这些资源，显示 UI 就没有任何意义。在这种情况下，我们可以推迟显示应用程序的 UI，直到我们的资源被获取并准备好。

```
installSplashScreen().setKeepVisibleCondition {
    mainViewModel.isScreenLoading.value
}
```

## setKeepVisibleCondition

这个方法接受一个回调函数，该函数返回一个布尔值来决定是否保持屏幕打开。True 值表示启动画面应该保持原样，False 值表示关闭启动画面。

SplashScreen API 会定期调用这个回调来检查是保留闪屏还是解除闪屏。

## 闪屏消失时动画显示屏幕内容

```
installSplashScreen()
.setOnExitAnimationListener { splashScreenViewProvider ->
    // Animation code
}
```

# **临时演员**

让我们讨论如何创建闪屏图像。

![](img/3e4d91dc74a8482717557d8771f0c3c5.png)

正如我们在闪屏图标中看到的，`section 1`被`section 3`屏蔽了

如果我们按原样应用我们的图标，它会缩小和伸展。

那么我们如何解决这个问题呢？我们需要创建一个图标来迎合这种掩盖填充。

假设我们有一个`54x54`的闪屏图标，那么我们将创建一个大小为`108x108`(实际图标的两倍)的包装层，并将图标内容放在它的中心。

## 例子

```
<?xml version="1.0" encoding="utf-8"?>
<vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:name="vector"
    android:width="108dp"
    android:height="108dp"
    android:viewportWidth="108"
    android:viewportHeight="108">
    <!--Optional background spanning full 108x108 dp-->
    <path
        android:name="background"
        android:pathData="M 0 0 L 108 0 L 108 108 L 0 108 Z"
        android:fillColor="@color/splash_icon_background"
        android:strokeWidth="1"/>
   <!--Splash screen icon placed in the centre of the screen-->
    <group
        android:name="group_icon"
        android:pivotX="54"
        android:pivotY="54">
        <path
            android:name="path_pi"
            android:pathData="M 30 44.591 L......."
            android:fillColor="@color/splash_icon_color"
            android:strokeWidth="1"/>
    </group>
</vector>
```

就这样了，伙计们。感谢阅读。

# 源代码

[](https://github.com/sridhar-sp/android-splash-screen-demo) [## GitHub-srid har-sp/Android-splash-screen-demo:演示各种特性的示例应用程序…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/sridhar-sp/android-splash-screen-demo)