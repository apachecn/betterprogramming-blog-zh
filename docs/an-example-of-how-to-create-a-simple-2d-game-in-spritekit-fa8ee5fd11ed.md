# 如何在 SpriteKit 中创建简单的 2D 游戏

> 原文：<https://betterprogramming.pub/an-example-of-how-to-create-a-simple-2d-game-in-spritekit-fa8ee5fd11ed>

## 学习如何编码是一个过程

编程很难。弄清楚如何让你的代码工作有时会令人沮丧。有时，你会开始怀疑自己是否能做到。学习编码可能看起来令人畏惧，但是迈出第一步很容易。获取任何新技能的知识都需要努力，学习如何编码也不例外。随着时间和努力，任何人都可以成为程序员。

## 什么是 SpriteKit？

SpriteKit 是苹果通过 Swift 为 iOS 创建 2D 游戏的框架，Swift 是苹果开发应用的专有语言。我做了这个简单的游戏，遵循了保罗·哈德森在 Swift 网站上的教程。该游戏是 Paul 网站上 Swift 天系列的一部分。如果你对 iOS 开发或编程感兴趣，这是一个很好的资源。本文的目的是从技术角度展示这个游戏，并教你一点关于 SpriteKit 的知识。

## 我们如何让球有弹性？

```
**let** ball = SKSpriteNode(imageNamed: "ballRed")ball.physicsBody = SKPhysicsBody(circleOfRadius: ball.size.width / 2.0)ball.physicsBody?.restitution = 0.4ball.physicsBody?.contactTestBitMask = ball.physicsBody?.collisionBitMask ?? 0ball.position = locationball.name = "ball"addChild(ball)
```

这里我们创建了变量“ball”的一个`SKSpriteNode`(sk node 的子类，是 SpriteKit 的基础构造块)。

接下来，我们给一个`SKPhysicsBody`赋值一个 circleOfRaidus 初始化器，这将赋予我们的球某种类型的物理特性。之后，我们添加恢复到我们的物理身体，给我们的球一些弹性。

接下来，我们添加一个`contactTestBitMask`，并将其设置为`collisionBitMask`的值。这会告诉我们的代码让我们知道游戏中发生的每一次碰撞。`ball.position = location`语法告诉我们的游戏在我们点击屏幕的位置放下一个球，而`ball.name = ball`语法告诉我们在代码中创建的所有 sprite 节点中，我们知道哪些是球。

最后，我们将`addChild`函数添加到“ball”中，这将把节点添加到我们的场景中。

## 当球击中插槽时会发生什么？

```
**func** collision(between ball: SKNode, object: SKNode) {**if** object.name == "good"{destroy(ball: ball)score += 1} **else** **if** object.name == "bad"{destroy(ball: ball)score -= 1}}**func** destroy(ball: SKNode) {**if** **let** fireParticles = SKEmitterNode(fileNamed: "FireParticles") {fireParticles.position = ball.positionaddChild(fireParticles)}ball.removeFromParent()}
```

这个代码告诉我们的游戏要么在球击中绿色插槽时给玩家加分，要么在球击中红色插槽时扣分。如果球击中红色插槽，将播放一个火粒子动画。

这都是通过函数来完成的，函数是一组规则，告诉应用程序执行一项任务。“碰撞”函数内部是一个 if-else 语句。If-else 语句告诉应用程序在满足特定条件时运行一段代码。

如果一个条件为真，那么代码将运行，否则，如果条件为假，它将不会运行。一旦球击中插槽，调用“销毁”功能，球将从我们的场景中移除。

## 给我们的玩家制造障碍

```
**let** size = CGSize(width: Int.random(**in**: 16...128), height: 16)**let** box = SKSpriteNode(color: UIColor(red: CGFloat.random(**in**: 0...1), green: CGFloat.random(**in**: 0...1), blue: CGFloat.random(**in**: 0...1), alpha: 1), size: size)box.zRotation = CGFloat.random(**in**: 0...3)box.position = locationbox.physicsBody = SKPhysicsBody(rectangleOf: box.size)box.physicsBody?.isDynamic = **false**addChild(box)
```

这段代码允许我们在每次点击屏幕时创建不同长度和颜色的盒子。

`CGSize`创建不同大小的精灵节点，然后我们使用一个`SKSpriteNode`来创建不同颜色的节点。然后我们将`zRotation`添加到我们的“盒子”变量中，这将允许我们的盒子在屏幕上旋转。

然后我们添加一个`physicsBody`和一个`rectangleOf`初始化器来制造障碍。之后，我们将`isDynamic`属性添加到我们的物理体中，并将其设置为 false。这将允许我们的障碍上有物理，但一旦一个球与它们碰撞，它们将不会移动。

如果你刚刚开始学习编码，Swift 是一个很好的学习语言，如果你想在 iOS 中开发游戏，SpriteKit 是一个很好的工具。我希望这篇文章能给你提供信息，帮助你以任何方式学习编程。