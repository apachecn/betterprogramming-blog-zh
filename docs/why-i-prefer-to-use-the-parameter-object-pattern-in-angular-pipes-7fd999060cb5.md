# 为什么我喜欢在角形管道中使用参数对象模式

> 原文：<https://betterprogramming.pub/why-i-prefer-to-use-the-parameter-object-pattern-in-angular-pipes-7fd999060cb5>

## 而不是直接传递参数

![](img/9d620be7d258efe67ae34f5488f43b94.png)

[T K](https://unsplash.com/@realaxer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/pipe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

我喜欢使用参数对象模式来减少传递给函数或方法的参数数量。我第一次从[干净代码:敏捷软件工艺手册](https://www.amazon.ca/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)中了解到这种模式。干净的代码甚至暗示三个或更多的参数对于一个函数来说太多了。

因此，如果有三个以上的参数，在将它们传递给函数之前，将这些参数封装到一个对象中是有意义的。

在这篇文章中，我将首先剖析我们通常如何实现一个直接传递参数的角形管道。然后，我们将介绍如何使用参数对象模式来实现它。

# 一个例子-战斗机剖面管

我们将在整篇博文中使用这个例子。例如，给定一个具有以下数据的 MMA 战斗机剖面:

*   姓名:Khabib Nurmagomedov
*   重量:155 磅
*   身高:158 厘米
*   胜数:29
*   损失:0

我们正在实现一个角度管道，将上述数据格式化为单行文本:

Khabib Nurmagomedov，155 磅，178 厘米，29 胜 0 负

我将把这个管道称为— `fighterProfile`。让我们看一下例子。

# 使用参数

如果我们使用参数，让我们从管道的转换方法开始。我们将`name`作为第一个参数，随后的参数将是`weight`、`height`、`wins`和`losses`。`format`方法将一个字符串附加到数字参数上，如`height`、`weight`、`wins`和`losses`。

战斗机剖面管上的斯塔克布利兹由作者。

## 单人战斗机剖面图

为了使用上面的`fighterProfile`管道，我们将参数直接传递给我们的模板，如下所示。

```
{{ 'Khabib Nurmagomedov' | fighterProfile: 29: 0: 155: 178 }}
```

输出将是:

```
Khabib Nurmagomedov, 155 lbs, 178 cm, 29 wins, 0 losses
```

## 一系列战斗机简介

现在让我们来看一个更真实的例子，一组战斗机的外形如下:

作者在 Stackblitz 上的一系列战斗机简介。

在我的示例代码中，我将`legacyFighterProfiles` 保存在一个单独的文件中。所以我们需要先把它赋给组件的属性。

```
this.legacyFightersList = legacyFighterProfiles;
```

我们将遍历`legacyFighterProfilesList`并为每个战斗机剖面应用管道。

```
<ul>
      <li *ngFor="let fighter of legacyFightersList">
        {{ fighter.bio.name | fighterProfile:
        fighter.mmaRecord.wins:
        fighter.mmaRecord.losses:
        fighter.bio.weight:
        fighter.bio.height }}
      </li>
</ul>
```

上面的代码应该输出:

*   康纳·麦格雷戈，22 胜 6 负，155 磅，175 厘米
*   哈比比，29 胜 0 负，155 磅，178 厘米
*   达斯汀·普瓦里耶，28 胜 6 负，155 磅，175 厘米

您可以在 StackBlitz 中找到完整的运行示例:

作者在 Stackblitz 上运行代码。

# 使用接口的参数对象模式

如果我们想使用参数对象模式，我们必须将整个战斗机配置文件作为单个参数传递。不像在我们之前的例子中，每个战斗机配置文件属性都是它的参数。我们将从声明我们的`FighterProfile`接口开始。

```
export interface FighterProfile {
  name: string;
  weight: number;
  height: number;
  wins: number;
  losses: number;
}
```

我们的 pipe 类中的`transform`方法将接受一个`fighterProfile`参数并返回格式化的字符串。格式方法将一个字符串附加到我们的`fighterProfile`接口的数值字段中。

战斗机剖面管上的斯塔克布利兹由作者。

## 单人战斗机剖面图

为了使用这个`fighterProfile`管道，我们可以用`FighterProfile`类型初始化一个属性。

```
this.singleProfile = {
 name: 'Khabib Nurmagomedov',
 wins: 29,
 losses: 0,
 weight: 155,
 height: 178
};
```

在我们的模板中，我们将把`singleProfile`属性作为单个参数传递给我们的`fighterProfile`管道。

```
{{ singleProfile | fighterProfile }}
```

我们的输出将是:

```
Khabib Nurmagomedov, 155 lbs, 178 cm, 29 wins, 0 losses
```

## 一系列战斗机简介

我们将使用上一个例子中的同一个数组。

作者在 StackBlitz 上的一系列战斗机简介。

让我们首先创建一个方法，将 fighter profile 字段转换成`FighterProfile`类型。该方法将接受一个`legacyFighterProfile` 参数。它将使用一些`legacyFighterProfile` 字段来返回`FighterProfile`类型。

```
fighterProfile(legacyFighterProfile: LegacyFighterProfile): FighterProfile {
    return {
      name: legacyFighterProfile.bio.name,
      weight: legacyFighterProfile.bio.weight,
      height: legacyFighterProfile.bio.height,
      wins: legacyFighterProfile.mmaRecord.wins,
      losses: legacyFighterProfile.mmaRecord.losses
    } as FighterProfile;
}
```

为了在我们的模板中使用这个管道，将`legacyFighterProfiles`值赋给我们组件的`legacyFightersList`属性。

```
this.legacyFightersList = legacyFighterProfiles;
```

然后遍历模板中的列表。将管道应用于每个`fighterProfile`数组元素。

```
<ul>
      <li *ngFor="let fighter of legacyFightersList">
        {{ fighterProfile(fighter) | fighterProfile}}
      </li>
</ul>
```

这比使用参数更具可读性。然而，由于[角度变化检测如何工作](https://blog.angular-university.io/how-does-angular-2-change-detection-really-work/)，这将不必要地调用`fighterProfile()` 方法很多次，而不是应该调用的*。*

更好的解决方案是去掉`fighterProfile()`方法，直接在模板中声明`FighterProfile` 对象。我们不需要额外调用组件的方法:

```
<ul>
    <li *ngFor="let fighter of legacyFightersList">
        {{
        { name: fighter.bio.name,
        wins: fighter.mmaRecord.wins,
        losses: fighter.mmaRecord.losses,
        weight: fighter.bio.weight,
        height: fighter.bio.height } | fighterProfile }}
    </li>
</ul>
```

您可以在 StackBlitz 中找到完整的运行示例:

作者在 Stackblitz 上运行代码。

# 结论

在我们的例子中去掉了我们的`fighterProfile()`方法之后，看起来我们实现了 parameter 对象模式，就像在模板中使用参数一样。

然而，参数对象模式实现的可读性稍强一些。因为我们很清楚要传递给管道的参数是什么，也就是说，我们知道是否要传递`weight`、`height`、`wins`和`losses`值。

```
{       
        name: fighter.bio.name,
        wins: fighter.mmaRecord.wins,
        losses: fighter.mmaRecord.losses,
        weight: fighter.bio.weight,
        height: fighter.bio.height 
}  | fighterProfile
```

如果你和直接传递参数相比。用冒号来传递这么多参数感觉不自然。不像前面的例子那么容易理解。

```
fighter.bio.name | fighterProfile:
        fighter.mmaRecord.wins:
        fighter.mmaRecord.losses:
        fighter.bio.weight:
        fighter.bio.height
```

感谢阅读。