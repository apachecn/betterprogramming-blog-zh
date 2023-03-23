# 在 Android 中配置预定通知

> 原文：<https://betterprogramming.pub/scheduled-notifications-in-android-2055356fb4f5>

## 为 Android 12 及更高版本准备好您的应用

![](img/8fb3dd1b14470d9471aeecbe633d8408.png)

由 [Roman Synkevych](https://unsplash.com/@synkevych?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将使用 android 中的警报管理器来实现预定通知。除此之外，我们还将探索 android 系统在通知和警报方面的行为变化。

# 安排警报

让我们从调度警报开始，`[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager)`类帮助我们执行应用程序范围之外的基于时间的操作。假设您可以安排一个闹钟，提醒用户在一天中的特定时间服药。

您也可以按选定的时间间隔安排重复提醒。这在各种情况下都很方便，比如提醒用户吃药或每隔一段时间补充水分。

## 一次性警报

让我们从简单的一次性(非重复)警报开始。要设置警报，首先我们需要创建如下所示的`[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager)`类实例:

```
private var alarmMgr: AlarmManager? = nullalarmMgr = context.getSystemService(Context.ALARM_SERVICE) as? AlarmManager?
```

然后我们需要创建一个调用`BroadcastReceiver`的待定意图，在这里我们可以执行期望的操作，比如在我们的例子中向用户显示通知。现在，让我们创建一个简单的名为`AlarmReceiver`的`BroadcastReceiver`，如下所示:

```
class **AlarmReceiver** : BroadcastReceiver() {

    override fun onReceive(p0: Context?, p1: Intent?) {
        *TODO*("Not yet implemented")
    }

}
```

现在是时候创建待定意图来触发`**AlarmReceiver**` **。**看一看:

```
val alarmIntent = Intent(this, **AlarmReceiver**::class.*java*)
val pendingIntent = **PendingIntent.getBroadcast**(
    context,
    requestCode,
    alarmIntent,
    PendingIntent.*FLAG_UPDATE_CURRENT*
)
```

最后，是时候设置闹钟了。为此，我们需要三样东西，警报类型、以毫秒为单位的触发时间和未决意图。后两种大多不言自明，而对于报警类型，`[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager)`提供了四种类型:

*   `[ELAPSED_REALTIME](https://developer.android.com/reference/android/app/AlarmManager#ELAPSED_REALTIME)`—根据设备启动后的时间触发待定意向，但不唤醒设备。经过的时间包括设备睡眠期间的任何时间。
*   `[ELAPSED_REALTIME_WAKEUP](https://developer.android.com/reference/android/app/AlarmManager#ELAPSED_REALTIME_WAKEUP)`—唤醒设备，并在设备启动后经过给定时间后触发待定意向。
*   `[RTC](https://developer.android.com/reference/android/app/AlarmManager#RTC)`—在给定时间触发未决意图，但不唤醒设备。
*   `[RTC_WAKEUP](https://developer.android.com/reference/android/app/AlarmManager#RTC_WAKEUP)`—在给定时间唤醒设备以调用未决意向。

让我们设置一个一分钟后触发的闹钟。

```
alarmMgr?.**set**(
        AlarmManager.ELAPSED_REALTIME_WAKEUP,
        **SystemClock.elapsedRealtime() + 60 * 1000**,
        alarmIntent
)
```

## 重复报警

安排重复提醒与一次性提醒非常相似。唯一的区别是提及警报之间的间隔，并使用`setRepeating`而不是`set`。看一看:

```
alarmMgr?.**setRepeating**(
        AlarmManager.RTC_WAKEUP,
        **SystemClock.elapsedRealtime() + 60 * 1000**,,
        **1000 * 60 * 15,**
        alarmIntent
)
```

上面这段代码在一分钟后触发警报，并每隔 15 分钟重复一次警报。

# 取消警报

在重复报警的情况下，取消报警是一个至关重要的功能。假设如果用户已经完成了这种药物的疗程，他不再需要提醒，那么在重复提醒的情况下，给用户一个取消选项是很重要的。

取消任何警报非常简单，您需要调用 cancel 函数并传递我们不想再触发的未决意图。看一看:

使用`getService`函数，我们可以获得我们不再想要触发的未决意图，如果未决意图不存在，这可能是`null`。请求代码对于确定正确的待定意向至关重要。

# 计划通知

现在我们已经完成了闹钟的安排，系统将会在指定的时间触发`AlarmReceiver`，下一个任务是触发来自`BroadcastReceiver`的通知。

我们可以通过创建待定意向时使用的意向来传递要在通知中显示的内容。然后在`onReceive`函数中，我们从参数中的意图中提取数据。看一看:

# 目标 SDK — 31

如果应用针对 android 12，那么开发者需要明确地处理一些情况，让我们开始`setExact`警报。

## 设置准确的警报类型

该系统会在未来某个精确的时刻触发精确的警报。如果目标是 android 12，那么开发者需要在使用它们之前检查应用程序是否有“警报和提醒”权限，否则它会抛出`SecurityException`。

作为第一步，我们需要在应用程序标签内的 android 清单文件中声明`SCHEDULE_EXACT_ALARM` 权限。

```
**<uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM"/>**
```

接下来，我们需要检查用户是否已经授予应用程序权限，为此我们需要检查两个条件:

1.  如果 OS 版本是 Android 12 以上。
2.  如果应用程序可以从报警管理器通过`canScheduleExactAlarms`访问。

张贴我们使用`setExact`警报代替`set`，如下所示:

```
alarmMgr?.**setExact**(
        AlarmManager.ELAPSED_REALTIME_WAKEUP,
        SystemClock.elapsedRealtime() + 60 * 2000,
        alarmIntent
)
```

# 准确和不准确的警报

对于不精确的警报，系统不能保证在精确的时间点发出警报。当它认为对电池最有效时，它会发出警报，通常它会在那个时候收集所有不准确的警报，并触发它们一次以保存电池健康。

在 Android 12 和更高版本中，`[set()](https://developer.android.com/reference/android/app/AlarmManager#set(int,%20long,%20android.app.PendingIntent))`、`[setInexactRepeating()](https://developer.android.com/reference/android/app/AlarmManager#setInexactRepeating(int,%20long,%20long,%20android.app.PendingIntent))`或`[setAndAllowWhileIdle()](https://developer.android.com/reference/android/app/AlarmManager#setAndAllowWhileIdle(int,%20long,%20android.app.PendingIntent))`可以在触发时间的 60 分钟内发出警报，除非有任何电池问题。如果要执行的任务优先级不高，最好使用不准确的警报。

至于准确的警报，他们主要关注的是在准确的时间发出警报，而不考虑系统的健康状况。`[setExactAndAllowWhileIdle()](https://developer.android.com/reference/android/app/AlarmManager#setExactAndAllowWhileIdle(int,%20long,%20android.app.PendingIntent))`在未来电池出现问题时，会在精确的时间发出警报。

强烈建议仅对面向用户的关键和时间敏感的中断使用精确警报。比如，如果你的应用程序是一个提醒或日历应用程序，通知用户即将发生的事件。

# 最佳实践

1.  在`BroadcastReceive`中只做局部工作。
2.  对任何非面向用户的事件使用不准确的警报。
3.  小心使用精确报警，因为即使系统处于打盹模式，它们也能发出警报。
4.  不要不必要地唤醒系统。
5.  使用工作管理器执行任何重复的异步工作。